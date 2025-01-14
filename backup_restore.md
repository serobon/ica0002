# RESTORATION PROCESS:

# MySQL:

1.  Run ansible playbook to deploy necessary infrastructure for working with backups.

        ansible-playbook infra.yaml
    This might take some while, so be patient please.

2.  Gain access to a machine with MySQL using SSH

        ssh ubuntu@193.40.156.67 -p(port)

    The (port) section should be replaced with a port of a machine with MySQL on it without a space or parenthesis(assuming you have access to the port somehow either from **hosts** file which should be on **first** line after **ansible_port=** or someone told you).

        EXAMPLE: ssh ubuntu@193.40.156.67 -p12345

3.  When in the machine, switch user to **backup** by running

        sudo su backup
    
    This should switch the user from `ubuntu@serobon-1` to `backup@serobon-1`. 

4.  Restore MySQL data from the backup:

        duplicity --no-encryption restore rsync://serobon@backup/mysql /home/backup/restore/
    
    This should tell you that it's copying the files to /home/backup/restore/ , and you can check that process was successful, look for **agama.sql** when you run

        ls -la /home/backup/restore/

5. To deploy the restored files, first run

        exit

    which will put you back to ubuntu user, and then run 

        sudo -i

    This will make you **root**. Finally run

        mysql agama < /home/backup/restore/agama.sql

    If this executes with no errors, restoration is complete.


# InfluxDB:

1.  Run ansible playbook to deploy necessary infrastructure for working with backups.

        ansible-playbook infra.yaml

    This might take some while, so be patient please. 

2.  Gain access to a machine with InfluxDB using SSH

        ssh ubuntu@193.40.156.67 -p(port)

    The (port) section should be replaced with a port of a machine with MySQL on it without a space or parenthesis(assuming you have access to the port somehow either from **hosts** file which should be on **third** line after **ansible_port=** or someone told you).

        EXAMPLE: ssh ubuntu@193.40.156.67 -p12345

3.  When in the machine, switch user to **backup** by running

        sudo su backup
    
    This should switch the user from `ubuntu@serobon-1` to `backup@serobon-1`. 

4.  Run as backup user

        duplicity --no-encryption restore rsync://serobon@backup/influxdb /home/backup/restore/

    This should tell you that it's copying the files to /home/backup/restore/ , and you can check that process was successful, look for 3 files ending with `.manifest`, `.meta` and `.s2.tar.gz` when you run

        ls -la /home/backup/restore/

5.  Restoring backup requires you to be root

        exit
    
    to return to user `ubuntu` and run

        sudo -i

    to become root. First Telegraf service needs to be stopped

        service telegraf stop

    and old database deleted

        influx -execute 'DROP DATABASE telegraf'

    and finally you can deploy the data to InfluxDB

        influxd restore -portable -database telegraf /home/backup/restore/

6.  Rerun ansible playbook by exiting out of all SSH sessions and running 

        ansible-playbook infra.yaml

    This will start the stopped telegraf service back.
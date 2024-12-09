Install and configure infrastructure with Ansible:

    ansible-playbook infra.yaml

Restore MySQL data from the backup:

    sudo -u backup duplicity --no-encryption full /home/backup/influxdb/ rsync://AlecSimp@backup.bindchilling.rice/influxdb
    <another-command>
    <yet-another-command>

<add a few words here how the result of backup restore can be checked>
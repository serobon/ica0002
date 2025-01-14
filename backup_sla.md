# Backup SLA

## Coverage

We back up services that satisfy at least one of these criteria:
- are the primary source of truth for particular data
- contain customer and/or client data
- are not feasible (or very costly) to restore by other means

Services that are backed up:
- MySQL
- InfluxDB


## Schedule

backups are created every day; it takes up to **2 hours** to create and store the backup.
- **Daily start at 21:50** for **MySQL**
- **Daily start at 22:10** for **InfluxDB**


All backups are started automatically by a **configured cron job**.

Backup RPO (recovery point objective) is:
 - **24 hours** for **MySQL**
 - **24 hours** for **InfluxDB**

## Storage

**MySQL** and **InfluxDB** backups are uploaded to the backup server.

**Application codebase** is mirrored to the internal Git server.

Backup data from both servers will be synchronized to an encrypted AWS S3 bucket in the future (work in progress).

## Retention

**Daily** backups are stored for **7 days**; **7 versions (recovery points)** are available to restore.

**Weekly** backups are stored for **4 weeks**; **4 versions** are available to restore

## Usability checks

**MySQL** backups are verified every **week** by **verifying that a recent backup is available by checking last backup time, restoring the backup and analyzing its integrity**.

**InfluxDB** backups are verified every **week** by **verifying that a recent backup is available by checking last backup time, restoring the backup and analyzing its integrity**.

**Application codebase** backups are verified every **week** by **verifying the renecy of GitHub version of the code**.

## Restore process

Service is recovered from the backup in case of an incident, and when the service cannot be restored in any other way.

RTO (recovery time objective) is:
 - **2 hours** for **MySQL**
 - **2 hours** for **InfluxDB**
 - **1 hour** for **application codebase and configuration files**

Detailed backup restore procedure is documented in the [backup_restore.md](./backup_restore.md).

# Setting Up Incremental MongoDB Backups with Rotation on Ubuntu

1. Install Required Packages:

```bash

sudo apt-get update
sudo apt-get install mongodb-org mongodump mongorestore
```

2. Configure MongoDB for `Oplog`:
   Ensure your MongoDB instance is configured with oplog enabled. This is typically the default setting. You can check your configuration file (/etc/mongod.conf) for the oplogSizeMB setting.
3. Create a Backup Script:
   Create a script (e.g., backup_mongodb.sh) to automate the backup process:

```Bash

#!/bin/bash

# Set backup directory
BACKUP_DIR="/path/to/your/backup/directory"
# Set oplog size (adjust as needed)
OPLOG_SIZE=1024

# Create backup directory if it doesn't exist
mkdir -p "$BACKUP_DIR"

# Full backup
mongodump -d your_database_name -o "$BACKUP_DIR/full_backup"

# Incremental backup
mongodump -d your_database_name -o "$BACKUP_DIR/incremental_backup" --oplogSize "$OPLOG_SIZE"

# Rotate backups (keep the last 3)
find "$BACKUP_DIR" -type d -mtime +3 -exec rm -rf {} \;
```

4. Schedule the Backup:
   Use a scheduling tool like cron to automate the backup process:

```Bash

0 3 * * * /path/to/your/backup_mongodb.sh

```

This command will run the script every day at 3 AM.

### Additional Considerations:

1. `Compression`: Compress the backup files to save storage space:

```Bash
gzip -9 "$BACKUP_DIR/full_backup/*"
gzip -9 "$BACKUP_DIR/incremental_backup/*"

```

2. `Encryption`: Encrypt the backups for added security.
3. `Remote Backup`: Transfer the backups to a remote storage location using tools like rsync or cloud storage services.
4. `Testing Restores`: Regularly test your backups by restoring them to a separate MongoDB instance.
5. `Monitoring`: Monitor the backup process and log any errors or failures.

> Remember to replace your_database_name and /path/to/your/backup/directory with your actual database name and desired backup location.

By following these steps and customizing the script to your specific needs, you can effectively implement an incremental backup strategy for your MongoDB database on Ubuntu.

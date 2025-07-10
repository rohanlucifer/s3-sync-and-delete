📁 S3 Sync and Local File Cleanup Automation

This project provides a robust Bash script to safely sync two local directories to Amazon S3 and conditionally delete files older than 90 days only if both sync operations succeed. It also includes a utility to audit uploaded file history in S3 by date and folder.
🔧 Components
1. s3-sync-local-delete.sh

Automates:

    Two-way sync from:

        /efs/pulse-home-3/hilton-ftp → s3://hilton-data-utility-onq-folders-backup/hilton-ftp/

        /efs/pulse-home-3/processed-files → s3://hilton-data-utility-onq-folders-backup/processed-files/

    Verifies both S3 syncs complete successfully.

    If successful, deletes local files older than 90 days in both folders.

    Logs all actions to /var/log/s3-sync-local-delete.log.

2. check_data_upload_s3

Generates a summary of file upload activity in your S3 bucket by folder and date.
📜 How It Works
🔁 S3 Sync Phase

    Uses aws s3 sync to transfer local files to respective S3 locations.

    If either sync fails, script halts—no files are deleted.

🧹 Local Cleanup Phase

    If both syncs succeed, uses find to delete files older than 90 days.

    Deletion stats (before and after) are logged for transparency.

✅ Requirements

    AWS CLI configured with necessary permissions

    IAM permissions required:

        s3:PutObject, s3:GetObject, s3:ListBucket, s3:DeleteObject

    Linux environment with Bash and find

🚀 Usage
One-Time Execution
```
chmod +x s3-sync-local-delete.sh
./s3-sync-local-delete.sh

Schedule via Cron (Optional)

To run daily at 2 AM:

0 2 * * * /path/to/s3-sync-local-delete.sh
```
📊 Check S3 Upload History

Run the included script to list the upload count by folder and date:
```
chmod +x check_data_upload_s3
./check_data_upload_s3

Output Example:

FOLDER                   DATE         COUNT
hilton-ftp              2025-07-08   320
processed-files         2025-07-08   122
```

📂 Sample File Stats Commands
Total files:
```
find /efs/pulse-home-3/hilton-ftp -type f | wc -l
find /efs/pulse-home-3/processed-files -type f | wc -l

Files older than 90 days:

find /efs/pulse-home-3/hilton-ftp -type f -mtime +90 | wc -l
find /efs/pulse-home-3/processed-files -type f -mtime +90 | wc -l
```
📁 Folder Structure

script-s3-sync/
├── s3-sync-local-delete.sh      # Main automation script
├── check_data_upload_s3         # S3 audit utility
├── commands                     # Handy shell commands for manual inspection
└── README.md                    # Project documentation

🛡️ Safety Features

    No deletion occurs unless both syncs succeed.

    Full logging to /var/log/s3-sync-local-delete.log.

    Modular function process_directory() for easy expansion.

📞 Contact

For questions, feel free to reach out to the script maintainer.

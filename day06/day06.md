# Day 06: Create a Cron Job

## Task Description
Install the cronie package on Nautilus app servers, start the crond service, and test a sample cron job that executes every 5 minutes.

## Key Requirements
- Install cronie on all app servers
- Start and enable the crond service
- Add a cron entry for root: `*/5 * * * * echo hello > /tmp/cron_text`

## Solution Commands

### Installation (RHEL/CentOS):
```bash
sudo yum install -y cronie
```

### Installation (Debian/Ubuntu):
```bash
sudo apt-get install -y cron
```

### Start and enable service (RHEL/CentOS):
```bash
sudo systemctl start crond
sudo systemctl enable crond
```

### Start and enable service (Debian/Ubuntu):
```bash
sudo systemctl start cron
sudo systemctl enable cron
```

### Add cron entry:
```bash
(sudo crontab -l 2>/dev/null; echo "*/5 * * * * echo hello > /tmp/cron_text") | sudo crontab -
```

### Verification:
```bash
sudo systemctl status crond  # or cron
sudo crontab -l
cat /tmp/cron_text
```

## Cron Syntax Explained

```
*/5 * * * * echo hello > /tmp/cron_text
│   │ │ │ │
│   │ │ │ └─── Day of week (0-7, Sunday = 0 or 7)
│   │ │ └───── Month (1-12)
│   │ └─────── Day of month (1-31)
│   └───────── Hour (0-23)
└─────────── Minute (0-59)
```

- `*/5` means "every 5 minutes"

## Notes
- The cron job may require up to 5 minutes for initial execution
- Platform considerations: For Debian/Ubuntu systems, use the cron service instead of crond
- The automated script preserves existing crontab entries
- Users can remove the test entry using grep filtering if needed
- Common cron schedules:
  - `0 * * * *` - Every hour
  - `0 0 * * *` - Daily at midnight
  - `0 0 * * 0` - Weekly on Sunday

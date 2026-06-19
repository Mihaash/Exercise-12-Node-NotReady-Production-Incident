# Configure Log Rotation for Kubernetes Container Logs

Create a logrotate configuration file:

```bash
sudo vim /etc/logrotate.d/container-logs
```

Add the following configuration:

```conf
/var/log/containers/*.log {
    daily
    rotate 7
    compress
    missingok
    notifempty
    copytruncate
}
```

---

## Explanation

### `/var/log/containers/*.log`

Apply these rules to all log files inside:

```text
/var/log/containers/
```

Examples:

```text
/var/log/containers/nginx.log
/var/log/containers/payment.log
/var/log/containers/mysql.log
```

---

## `daily`

```conf
daily
```

Rotate log files once every day.

For example:

Today:

```text
payment.log
```

Tomorrow:

The old file becomes:

```text
payment.log.1
```

and a new empty file is created:

```text
payment.log
```

---

## `rotate 7`

```conf
rotate 7
```

Keep the last seven rotated log files.

Example:

```text
payment.log
payment.log.1
payment.log.2
payment.log.3
payment.log.4
payment.log.5
payment.log.6
payment.log.7
```

When the eighth rotation occurs:

```text
payment.log.7
```

is deleted automatically.

Thus, only seven days of logs are retained.

---

## `compress`

```conf
compress
```

Compress old log files using gzip.

Instead of:

```text
payment.log.1
payment.log.2
```

you get:

```text
payment.log.1.gz
payment.log.2.gz
```

This saves a significant amount of disk space.

Example:

| File | Size |
|------|------:|
| payment.log.1 | 1 GB |
| payment.log.1.gz | 100 MB |

---

## `missingok`

```conf
missingok
```

Do not display an error if a log file is missing.

Example:

If:

```text
/var/log/containers/mysql.log
```

does not exist, logrotate continues processing the remaining files without failing.

---

## `notifempty`

```conf
notifempty
```

Do not rotate empty log files.

Example:

If:

```text
payment.log
```

has a size of:

```text
0 bytes
```

it will not be rotated.

---

## `copytruncate`

```conf
copytruncate
```

Copy the contents of the current log file to a rotated file and then truncate the original file.

Example:

Before rotation:

```text
payment.log
```

After rotation:

```text
payment.log.1.gz
payment.log
```

The application continues writing to:

```text
payment.log
```

without needing to be restarted.

---

# Verify Configuration

Test the configuration:

```bash
sudo logrotate -d /etc/logrotate.d/container-logs
```

Force a rotation:

```bash
sudo logrotate -f /etc/logrotate.d/container-logs
```

Verify:

```bash
ls -lh /var/log/containers/
```

Example:

```text
payment.log
payment.log.1.gz
payment.log.2.gz
nginx.log
nginx.log.1.gz
mysql.log
mysql.log.1.gz
```

---

## Workflow

```text
Container Logs
       ↓
Logrotate
       ↓
Daily Rotation
       ↓
Keep Last 7 Days
       ↓
Compress Old Logs
       ↓
Prevent Disk Exhaustion
```

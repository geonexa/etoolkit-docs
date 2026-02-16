# uWSGI Configuration

Guide for configuring uWSGI application server for EO-Toolkit.

## Overview

uWSGI serves the Django application and communicates with Apache via uwsgi protocol.

## Installation

uWSGI is typically installed via pip in the virtual environment:

```bash
cd /path/to/etoolkit/webapp
source venv/bin/activate
pip install uwsgi
```

## Configuration

### Systemd Service File

Create service file: `etoolkit/etoolkit_uwsgi.service`

### Example Service File

```ini
[Unit]
Description=uWSGI instance to serve etoolkit
After=network.target

[Service]
User=www-data
Group=www-data
WorkingDirectory=/path/to/etoolkit
Environment="PATH=/path/to/etoolkit/webapp/venv/bin"
ExecStart=/path/to/etoolkit/webapp/venv/bin/uwsgi --ini /path/to/etoolkit/etoolkit/uwsgi.ini

[Install]
WantedBy=multi-user.target
```

### uWSGI INI File

Create: `etoolkit/uwsgi.ini`

```ini
[uwsgi]
project = etoolkit
base = /path/to/etoolkit

chdir = %(base)
module = %(project).wsgi:application
master = true
processes = 4
threads = 2

vacuum = true
die-on-term = true

socket = /path/to/etoolkit/etoolkit.sock
chmod-socket = 666
uid = www-data
gid = www-data

logto = /path/to/etoolkit/log/etoolkit.log
```

## Setup

### Create Socket Directory

```bash
mkdir -p /path/to/etoolkit/etoolkit
chown www-data:www-data /path/to/etoolkit/etoolkit
```

### Create Log Directory

```bash
mkdir -p /path/to/etoolkit/log
chown www-data:www-data /path/to/etoolkit/log
```

### Link Service File

```bash
sudo ln -s /path/to/etoolkit/etoolkit/etoolkit_uwsgi.service /etc/systemd/system/
```

### Enable and Start

```bash
sudo systemctl daemon-reload
sudo systemctl enable etoolkit_uwsgi.service
sudo systemctl start etoolkit_uwsgi.service
```

## Management

### Start Service

```bash
sudo systemctl start etoolkit_uwsgi.service
```

### Stop Service

```bash
sudo systemctl stop etoolkit_uwsgi.service
```

### Restart Service

```bash
sudo systemctl restart etoolkit_uwsgi.service
```

### Check Status

```bash
sudo systemctl status etoolkit_uwsgi.service
```

### View Logs

```bash
tail -f /path/to/etoolkit/log/etoolkit.log
```

## Configuration Options

### Processes and Threads

Adjust based on server resources:

```ini
processes = 4      # Number of worker processes
threads = 2        # Threads per process
```

### Socket Configuration

```ini
socket = /path/to/etoolkit/etoolkit.sock
chmod-socket = 666
```

### Logging

```ini
logto = /path/to/etoolkit/log/etoolkit.log
log-maxsize = 50000000
log-backupcount = 5
```

## Troubleshooting

### Service Won't Start

- Check configuration file paths
- Verify user permissions
- Check log file for errors
- Verify virtual environment path

### Permission Errors

```bash
sudo chown -R www-data:www-data /path/to/etoolkit
sudo chmod -R 755 /path/to/etoolkit
```

### Socket Issues

- Check socket file permissions
- Verify Apache can access socket
- Check socket file exists
- Restart both uWSGI and Apache

### High Memory Usage

- Reduce number of processes
- Reduce threads per process
- Monitor memory usage
- Consider server upgrade

## Performance Tuning

### Worker Configuration

```ini
# For CPU-bound tasks
processes = (2 * CPU cores) + 1
threads = 2

# For I/O-bound tasks
processes = CPU cores
threads = 4
```

### Memory Limits

```ini
limit-as = 512
reload-on-as = 400
```

### Harakiri (Request Timeout)

```ini
harakiri = 60
harakiri-verbose = true
```

## Reloading After Code Changes

### Graceful Reload

```bash
sudo systemctl reload etoolkit_uwsgi.service
```

### Full Restart

```bash
sudo systemctl restart etoolkit_uwsgi.service
```

## Monitoring

### Process Status

```bash
ps aux | grep uwsgi
```

### Socket Status

```bash
ls -l /path/to/etoolkit/etoolkit.sock
```

### Connection Count

Monitor Apache access logs for connection patterns.

---

Next: [Celery Setup](celery.md)

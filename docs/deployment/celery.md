# Celery Configuration

Guide for configuring Celery task queue for EO-Toolkit.

## Overview

Celery handles asynchronous tasks like report generation and long-running analyses.

## Prerequisites

- Redis installed and running
- Celery installed in virtual environment
- Database configured for results backend

## Redis Setup

### Install Redis

```bash
sudo apt-get install redis-server
```

### Start Redis

```bash
sudo systemctl start redis
sudo systemctl enable redis
```

### Verify Redis

```bash
redis-cli ping
# Should return: PONG
```

## Celery Installation

```bash
cd /path/to/etoolkit/webapp
source venv/bin/activate
pip install celery django-celery-results redis
```

## Configuration

### Celery Configuration File

File: `etoolkit/celery.py`

```python
from celery import Celery
import os

os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'etoolkit.settings')

app = Celery('etoolkit')
app.config_from_object('django.conf:settings', namespace='CELERY')
app.autodiscover_tasks()
```

### Django Settings

Add to `settings.py`:

```python
CELERY_BROKER_URL = 'redis://localhost:6379/0'
CELERY_RESULT_BACKEND = 'django-db'
CELERY_ACCEPT_CONTENT = ['json']
CELERY_TASK_SERIALIZER = 'json'
CELERY_RESULT_SERIALIZER = 'json'
CELERY_TIMEZONE = 'UTC'
```

## Systemd Service

### Create PID Directory

```bash
sudo mkdir /var/run/celery/
sudo chown -R etoolkit_user:etoolkit_user /var/run/celery/
```

### Service File

Create: `etoolkit/etoolkit_celery.service`

```ini
[Unit]
Description=Celery Service for etoolkit
After=network.target

[Service]
Type=forking
User=etoolkit_user
Group=etoolkit_user
WorkingDirectory=/path/to/etoolkit
EnvironmentFile=/path/to/etoolkit/etoolkit/celery.conf
ExecStart=/path/to/etoolkit/webapp/venv/bin/celery multi start worker1 \
    -A etoolkit \
    --pidfile=/var/run/celery/%n.pid \
    --logfile=/path/to/etoolkit/log/celery/%n.log \
    --loglevel=INFO \
    --concurrency=8 \
    --time-limit=3000
ExecStop=/path/to/etoolkit/webapp/venv/bin/celery multi stopwait worker1 \
    --pidfile=/var/run/celery/%n.pid
ExecReload=/path/to/etoolkit/webapp/venv/bin/celery multi restart worker1 \
    -A etoolkit \
    --pidfile=/var/run/celery/%n.pid \
    --logfile=/path/to/etoolkit/log/celery/%n.log \
    --loglevel=INFO \
    --concurrency=8 \
    --time-limit=3000

[Install]
WantedBy=multi-user.target
```

### Environment File (Optional)

Create: `etoolkit/celery.conf`

```ini
CELERY_BROKER_URL=redis://localhost:6379/0
CELERY_RESULT_BACKEND=django-db
```

## Setup

### Create Log Directory

```bash
mkdir -p /path/to/etoolkit/log/celery
chown -R etoolkit_user:etoolkit_user /path/to/etoolkit/log/celery
```

### Link Service File

```bash
sudo ln -s /path/to/etoolkit/etoolkit/etoolkit_celery.service /etc/systemd/system/
```

### Enable and Start

```bash
sudo systemctl daemon-reload
sudo systemctl enable etoolkit_celery.service
sudo systemctl start etoolkit_celery.service
```

## Management

### Start Service

```bash
sudo systemctl start etoolkit_celery.service
```

### Stop Service

```bash
sudo systemctl stop etoolkit_celery.service
```

### Restart Service

```bash
sudo systemctl restart etoolkit_celery.service
```

### Check Status

```bash
sudo systemctl status etoolkit_celery.service
```

### View Logs

```bash
tail -f /path/to/etoolkit/log/celery/worker1.log
```

## Monitoring

### Active Tasks

```bash
cd /path/to/etoolkit
source webapp/venv/bin/activate
celery -A etoolkit inspect active
```

### Registered Tasks

```bash
celery -A etoolkit inspect registered
```

### Worker Status

```bash
celery -A etoolkit inspect stats
```

## Configuration Options

### Concurrency

```bash
--concurrency=8  # Number of worker processes
```

### Time Limits

```bash
--time-limit=3000  # Hard time limit (seconds)
--soft-time-limit=2400  # Soft time limit (seconds)
```

### Logging

```bash
--loglevel=INFO  # DEBUG, INFO, WARNING, ERROR
--logfile=/path/to/log/celery/%n.log
```

## Troubleshooting

### Service Won't Start

- Check Redis is running
- Verify configuration paths
- Check log files
- Verify user permissions

### Tasks Not Processing

- Check Celery workers running
- Verify Redis connection
- Check task queue
- Review error logs

### High Memory Usage

- Reduce concurrency
- Adjust time limits
- Monitor worker processes
- Consider scaling

### Task Failures

- Check task logs
- Verify task code
- Check dependencies
- Review error messages

## Multiple Workers

### Starting Multiple Workers

```bash
celery multi start worker1 worker2 worker3 \
    -A etoolkit \
    --pidfile=/var/run/celery/%n.pid \
    --logfile=/path/to/etoolkit/log/celery/%n.log \
    --loglevel=INFO \
    --concurrency=4
```

### Stopping All Workers

```bash
celery multi stopwait worker1 worker2 worker3 \
    --pidfile=/var/run/celery/%n.pid
```

## Flower (Monitoring Tool)

### Install Flower

```bash
pip install flower
```

### Run Flower

```bash
celery -A etoolkit flower
```

Access at: `http://localhost:5555`

---

Next: [SSL Configuration](ssl.md)

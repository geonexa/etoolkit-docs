# Logs Management

This page describes where EO-Toolkit writes logs, how to view them, and how they are configured. Use this for debugging, monitoring, and routine operations.

Replace `/path/to/etoolkit` with your actual project path (e.g. `/home/ecduser/etoolkit`).

---

## Log locations overview

| Component | Log path | Purpose |
|-----------|----------|---------|
| **Application (uWSGI/Django)** | `/path/to/etoolkit/etoolkit/log/etoolkit.log` | Django app and uWSGI output |
| **Celery workers** | `/path/to/etoolkit/etoolkit/log/celery/worker1.log` (and worker2, …) | Async task execution, GEE jobs, reports |
| **Apache error** | `/var/log/apache2/etoolkit_error.log` | HTTP errors, proxy issues |
| **Apache access** | `/var/log/apache2/etoolkit_access.log` | HTTP requests (combined format) |

---

## Application log (uWSGI / Django)

- **Path:** `etoolkit/etoolkit/log/etoolkit.log`
- **Configured in:** `etoolkit/etoolkit.ini` → `daemonize = .../log/etoolkit.log`

### View in real time

```bash
tail -f /path/to/etoolkit/etoolkit/log/etoolkit.log
```

### View last lines

```bash
tail -n 200 /path/to/etoolkit/etoolkit/log/etoolkit.log
```

### Ensure directory exists

The log directory is created at install time (see [Installation Guide](../installation.md)). If missing:

```bash
mkdir -p /path/to/etoolkit/log
```

---

## Celery logs

- **Path:** `etoolkit/etoolkit/log/celery/worker1.log`, `worker2.log`, … (one per worker)
- **Configured in:** Celery systemd service and `celery.conf` (e.g. `CELERYD_LOG_FILE`, `CELERYD_LOG_DIR`)

### View a single worker in real time

```bash
tail -f /path/to/etoolkit/etoolkit/log/celery/worker1.log
```

### List and tail all Celery logs

```bash
ls -lh /path/to/etoolkit/etoolkit/log/celery/
for f in /path/to/etoolkit/etoolkit/log/celery/*.log; do echo "=== $f ==="; tail -n 20 "$f"; done
```

### Check recent activity across workers

```bash
tail -n 50 /path/to/etoolkit/etoolkit/log/celery/worker1.log
tail -n 50 /path/to/etoolkit/etoolkit/log/celery/worker2.log
```

Use these logs when debugging failed or stuck tasks (e.g. GEE analyses, report generation).

---

## Apache logs

- **Error log:** `/var/log/apache2/etoolkit_error.log`
- **Access log:** `/var/log/apache2/etoolkit_access.log`
- **Configured in:** Apache virtual host (e.g. `etoolkit.conf`) with `ErrorLog` and `CustomLog`

### View error log in real time

```bash
sudo tail -f /var/log/apache2/etoolkit_error.log
```

### View access log in real time

```bash
sudo tail -f /var/log/apache2/etoolkit_access.log
```

### Permissions

Apache runs as `www-data`; reading these logs usually requires `sudo` or membership in `adm`.


---

## Troubleshooting checklist

| Issue | Where to look |
|-------|----------------|
| App not responding, 502/503 | `etoolkit.log`, `etoolkit_error.log` |
| Login or auth errors | `etoolkit.log`, mail server logs |
| Async task failed or stuck | `log/celery/worker*.log` |
| Layer or WMS not loading | GeoServer logs, `etoolkit_error.log` |
| High traffic or slow requests | `etoolkit_access.log`, `etoolkit.log` |

# Deployment Guide

This guide covers deploying EO-Toolkit to production in a single place: Apache, uWSGI, Celery, Redis, and SSL.

## Deployment Architecture

- **Apache**: Web server (reverse proxy, static/media)
- **uWSGI**: WSGI application server (Django)
- **Celery**: Asynchronous task queue (reports, analyses)
- **Redis**: Message broker for Celery
- **PostgreSQL**: Database with PostGIS
- **GeoServer**: Spatial data server

Replace `/path/to/etoolkit` and `etoolkit.terrawatch.net` with your paths and domain.

---

## 1. Apache

### Install and enable modules

```bash
sudo apt-get install apache2
sudo apt install libapache2-mod-uwsgi
sudo a2enmod uwsgi
sudo a2enmod ssl
```

### Site configuration

Copy and edit the template, then enable the site:

```bash
sudo cp etoolkit/template_apache.conf /etc/apache2/sites-available/etoolkit.conf
sudo nano /etc/apache2/sites-available/etoolkit.conf
```

Example (HTTP redirect + HTTPS virtual host):

```apache
<VirtualHost *:80>
    ServerName etoolkit.terrawatch.net
    ServerAlias www.etoolkit.terrawatch.net
    Redirect permanent / https://etoolkit.terrawatch.net/
</VirtualHost>

<VirtualHost *:443>
    ServerName etoolkit.terrawatch.net
    ServerAlias www.etoolkit.terrawatch.net

    SSLEngine on
    SSLCertificateFile /etc/letsencrypt/live/etoolkit.terrawatch.net/fullchain.pem
    SSLCertificateKeyFile /etc/letsencrypt/live/etoolkit.terrawatch.net/privkey.pem
    Include /etc/letsencrypt/options-ssl-apache.conf

    DocumentRoot /path/to/etoolkit
    Alias /static /path/to/etoolkit/staticfiles
    Alias /media /path/to/etoolkit/media

    <Directory /path/to/etoolkit/staticfiles>
        Require all granted
    </Directory>
    <Directory /path/to/etoolkit/media>
        Require all granted
    </Directory>
    <Directory /path/to/etoolkit>
        <Files wsgi.py>
            Require all granted
        </Files>
    </Directory>

    WSGIDaemonProcess etoolkit python-home=/path/to/etoolkit/webapp/venv python-path=/path/to/etoolkit
    WSGIProcessGroup etoolkit
    WSGIScriptAlias / /path/to/etoolkit/etoolkit/wsgi.py

    Header always set X-Content-Type-Options "nosniff"
    Header always set X-Frame-Options "SAMEORIGIN"
    Header always set X-XSS-Protection "1; mode=block"

    ErrorLog ${APACHE_LOG_DIR}/etoolkit_error.log
    CustomLog ${APACHE_LOG_DIR}/etoolkit_access.log combined
</VirtualHost>
```

Enable site and reload:

```bash
sudo a2ensite etoolkit.conf
sudo apachectl configtest
sudo systemctl reload apache2
```

### Static and media

```bash
cd /path/to/etoolkit
source webapp/venv/bin/activate
python manage.py collectstatic --noinput

mkdir -p /path/to/etoolkit/media
chmod -R 755 /path/to/etoolkit/media
chown -R www-data:www-data /path/to/etoolkit/media
```

---

## 2. uWSGI

### Install

```bash
cd /path/to/etoolkit/webapp
source venv/bin/activate
pip install uwsgi
```

### uWSGI INI

Create `etoolkit/uwsgi.ini`:

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

### Systemd service

Create `etoolkit/etoolkit_uwsgi.service`:

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

Enable and start:

```bash
mkdir -p /path/to/etoolkit/log
sudo chown www-data:www-data /path/to/etoolkit/etoolkit /path/to/etoolkit/log
sudo ln -s /path/to/etoolkit/etoolkit/etoolkit_uwsgi.service /etc/systemd/system/
sudo systemctl daemon-reload
sudo systemctl enable etoolkit_uwsgi.service
sudo systemctl start etoolkit_uwsgi.service
```

---

## 3. Celery and Redis

### Redis

```bash
sudo apt-get install redis-server
sudo systemctl start redis
sudo systemctl enable redis
redis-cli ping
```

### Celery systemd service

Create PID and log dirs:

```bash
sudo mkdir /var/run/celery/
sudo chown -R etoolkit_user:etoolkit_user /var/run/celery/
mkdir -p /path/to/etoolkit/log/celery
chown -R etoolkit_user:etoolkit_user /path/to/etoolkit/log/celery
```

Create `etoolkit/etoolkit_celery.service` (replace `etoolkit_user` and paths):

```ini
[Unit]
Description=Celery Service for etoolkit
After=network.target

[Service]
Type=forking
User=etoolkit_user
Group=etoolkit_user
WorkingDirectory=/path/to/etoolkit
ExecStart=/path/to/etoolkit/webapp/venv/bin/celery multi start worker1 \
    -A etoolkit \
    --pidfile=/var/run/celery/%n.pid \
    --logfile=/path/to/etoolkit/log/celery/%n.log \
    --loglevel=INFO \
    --concurrency=8 \
    --time-limit=3000
ExecStop=/path/to/etoolkit/webapp/venv/bin/celery multi stopwait worker1 \
    --pidfile=/var/run/celery/%n.pid

[Install]
WantedBy=multi-user.target
```

Enable and start:

```bash
sudo ln -s /path/to/etoolkit/etoolkit/etoolkit_celery.service /etc/systemd/system/
sudo systemctl daemon-reload
sudo systemctl enable etoolkit_celery.service
sudo systemctl start etoolkit_celery.service
```

Ensure `settings.py` has Celery broker and result backend (e.g. Redis and django-db).

---

## 4. SSL (HTTPS)

### Let's Encrypt with Certbot

```bash
sudo apt update
sudo apt install certbot python3-certbot-apache
sudo certbot --apache -d etoolkit.terrawatch.net -d www.etoolkit.terrawatch.net
```

Follow prompts; Certbot configures auto-renewal. Test renewal:

```bash
sudo certbot renew --dry-run
```

### Manual certificates

Place certificate and key files, then in your Apache HTTPS virtual host set:

- `SSLCertificateFile` / path to certificate
- `SSLCertificateKeyFile` / path to private key
- `SSLCertificateChainFile` if required

Then reload Apache.



---

## 5. Service Management

### Start / stop / restart

```bash
sudo systemctl start etoolkit_uwsgi etoolkit_celery apache2
sudo systemctl stop etoolkit_uwsgi etoolkit_celery apache2
sudo systemctl restart etoolkit_uwsgi etoolkit_celery apache2
```

### Enable on boot

```bash
sudo systemctl enable etoolkit_uwsgi etoolkit_celery apache2
```

### Status

```bash
sudo systemctl status etoolkit_uwsgi
sudo systemctl status etoolkit_celery
sudo systemctl status apache2
```

---

## 6. Logs

- Application: `tail -f /path/to/etoolkit/log/etoolkit.log`
- Celery: `tail -f /path/to/etoolkit/log/celery/worker1.log`
- Apache: `sudo tail -f /var/log/apache2/etoolkit_error.log` and `etoolkit_access.log`

---

## 7. Troubleshooting

**Permission errors**: Ensure `www-data` (or your Apache user) can read the project and socket; e.g. `sudo chown -R www-data:www-data /path/to/etoolkit` and correct permissions on `etoolkit.sock`.

**uWSGI not starting**: Check paths in `uwsgi.ini` and the service file; confirm virtualenv path and `wsgi.py` location.

**Celery not processing**: Verify Redis is running (`redis-cli ping`), Celery service is up, and broker/backend in `settings.py` match your Redis URL.

**Apache 502 / no response**: Ensure uWSGI is running and the socket path in Apache matches `uwsgi.ini`; restart uWSGI and Apache.

**SSL errors**: Confirm certificate paths in Apache, `a2enmod ssl`, and certificate validity.

---
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

## 1. uWSGI


### uWSGI INI

Edit `etoolkit/etoolkit.ini`:

```ini
[uwsgi]
chdir           = /home/ecduser/etoolkit/etoolkit
module          = etoolkit.wsgi
home            = /home/ecduser/etoolkit/venv
env             = DJANGO_SETTINGS_MODULE=etoolkit.settings
master          = true
processes       = 5
threads         = 2
socket          = /home/ecduser/etoolkit/etoolkit/etoolkit.sock
chmod-socket    = 660
chown-socket    = ecduser:ecduser
vacuum          = true
daemonize       = /home/ecduser/etoolkit/etoolkit/log/etoolkit.log
post-buffering  = true
route-run       = harakiri:180
pidfile         = /home/ecduser/etoolkit/etoolkit/uwsgi.pid
```

### Systemd service

Edit `etoolkit/etoolkit_uwsgi.service`:

```ini
[Unit]
Description=uWSGI service for etoolkit app
After=network.target

[Service]
# user who owns the project
User=ecduser
Group=ecduser

# project directory
WorkingDirectory=/home/ecduser/etoolkit

# start uWSGI using the .ini file
ExecStart=/home/ecduser/etoolkit/venv/bin/uwsgi --ini /home/ecduser/etoolkit/etoolkit/etoolkit.ini

# auto restart if it crashes
Restart=always
RestartSec=5

# allow systemd to track uwsgi
KillSignal=SIGQUIT
Type=notify
NotifyAccess=all

[Install]
WantedBy=multi-user.target

```

uWSGI Deployment
  ```

  * copy the systemd configuration file
  sudo ln -s /home/ecduser/etoolkit/etoolkit/etoolkit_uwsgi.service /etc/systemd/system

  * Enable + Start uWSGI
  sudo systemctl daemon-reload
  sudo systemctl enable etoolkit_uwsgi.service
  sudo systemctl start etoolkit_uwsgi.service
  sudo systemctl status etoolkit_uwsgi.service

  * Restart Service
  sudo systemctl restart etoolkit_uwsgi.service

  ```

## 2. Celery Deployment
Edit `etoolkit/etoolkit_celery.service` :


```ini
[Unit]
Description=Celery Service for etoolkit app
After=network.target

[Service]
Type=forking
User=ecduser
Group=ecduser
EnvironmentFile=/home/ecduser/etoolkit/etoolkit/celery.conf
WorkingDirectory=/home/ecduser/etoolkit/etoolkit/
ExecStart=/bin/sh -c '${CELERY_BIN} -A ${CELERY_APP} multi start ${CELERYD_NODES} \
    --pidfile=${CELERYD_PID_FILE} --logfile=${CELERYD_LOG_FILE} \
    --loglevel=${CELERYD_LOG_LEVEL} ${CELERYD_OPTS}'
ExecStop=/bin/sh -c '${CELERY_BIN} multi stopwait ${CELERYD_NODES} \
    --pidfile=${CELERYD_PID_FILE} --loglevel=${CELERYD_LOG_LEVEL}'
ExecReload=/bin/sh -c '${CELERY_BIN} -A ${CELERY_APP} multi restart ${CELERYD_NODES} \
    --pidfile=${CELERYD_PID_FILE} --logfile=${CELERYD_LOG_FILE} \
    --loglevel=${CELERYD_LOG_LEVEL} ${CELERYD_OPTS}'
Restart=always

[Install]
WantedBy=multi-user.target

```

Enable and start:

```
  * Create all the stuff needed to run celery in deployment mode

  * create the pid directory
  sudo mkdir /var/run/celery/
  sudo chown -R ecduser:ecduser /var/run/celery/

  * copy the systemd configuration file
  sudo ln -s /home/ecduser/etoolkit/etoolkit/etoolkit_celery.service /etc/systemd/system

  <!-- EnvironmentFile=-/home/ecduser/etoolkit/etoolkit/celery.conf -->
  <!-- WorkingDirectory=/home/ecduser/etoolkit/etoolkit/ -->

  * modify the environment file if needed (for example the timeout for a single job set to 3000 seconds or number of concurrency set to 8)

  * reload the systemd files (this has been done everytime etoolkit_celery.service is changed)
  sudo systemctl daemon-reload

  * enable the service to be automatically start on boot
  sudo systemctl enable etoolkit_celery.service

  * Start the celery app
  sudo systemctl start etoolkit_celery.service

  * to look if everything is working properly you can
  sudo systemctl status etoolkit_celery.service

  ls -lh /home/etoolkit/etoolkit/log/celery/
  <!-- ls -lh /home/ecduser/etoolkit/etoolkit/log/celery/ -->
  
  tail -f /home/etoolkit/etoolkit/log/celery/worker1.log
  <!-- tail -f /home/ecduser/etoolkit/etoolkit/log/celery/worker1.log -->


```

## 3. Restart the celery and uWSGI in development after updates

```bash
* Reload the systemd files (this has been done everytime etoolkit_celery.service is changed)
sudo systemctl daemon-reload

* Restart Celery Service
sudo systemctl restart etoolkit_celery.service

* Stop Celery Service
sudo systemctl stop etoolkit_celery.service

* Start Celery Service
sudo systemctl start etoolkit_celery.service

* Verify Celery is Running Correctly
sudo systemctl status etoolkit_celery.service


* Kill Remaining Celery Processes
sudo pkill -9 -f 'celery worker'

* Ensure All Processes Are Stopped
ps aux | grep celery


* Monitoring Logs
tail -f /home/ecduser/etoolkit/log/celery/worker1-7.log
tail -f /home/ecduser/etoolkit/log/celery/worker1-6.log
tail -f /home/ecduser/etoolkit/log/celery/worker1.log



for file in /home/ecduser/etoolkit/log/celery/*.log; do
    echo "Checking $file"
    tail -n 20 $file
done


* Restart uWSGI
sudo systemctl restart etoolkit_uwsgi
```

---


## 4. Apache


### Site configuration

Copy and edit the template, then enable the site:

```bash
sudo cp etoolkit/template_apache.conf /etc/apache2/sites-available/etoolkit.conf
sudo nano /etc/apache2/sites-available/etoolkit.conf

```

Example (etoolkit.conf) (HTTP redirect + HTTPS virtual host):

```apache
<VirtualHost *:80>
    ServerName etoolkit.terrawatch.net
    Redirect permanent / https://etoolkit.terrawatch.net/
</VirtualHost>

<VirtualHost *:443>
    ServerName etoolkit.terrawatch.net


    <Directory /home/ecduser/etoolkit/etoolkit/etoolkit>
        <Files wsgi.py>
            Require all granted
        </Files>
    </Directory>

    <Location /static>
            SetHandler none
            Options -Indexes
    </Location>

    <Location /media>
            SetHandler none
            Options -Indexes
    </Location>

    Alias /media/ /home/ecduser/etoolkit/etoolkit/media/

    Alias /static/ /home/ecduser/etoolkit/etoolkit/static/

    <Directory /home/ecduser/etoolkit/etoolkit/>
            Require all granted
    </Directory>

    <Directory /home/ecduser/etoolkit/etoolkit/static>
            Options FollowSymLinks
            Order allow,deny
            Allow from all
    </Directory>

    <Directory /home/ecduser/etoolkit/etoolkit/media>
            Options FollowSymLinks
            Order allow,deny
            Allow from all
    </Directory>

    ProxyPass / http://127.0.0.1:8002/
    ProxyPassReverse / http://127.0.0.1:8002/

    #ProxyPass / unix:/home/ecduser/etoolkit/etoolkit/etoolkit.sock|uwsgi://localhost/
    # Proxying the connection to uWSGI
    

    ErrorLog ${APACHE_LOG_DIR}/etoolkit_error.log
    CustomLog ${APACHE_LOG_DIR}/etoolkit_access.log combined


</VirtualHost>

```

```

  * Enable uwsgi and ssl module in apache
  sudo a2enmod uwsgi
  sudo a2enmod ssl

  * Enable the virtual host with the following command:**
  sudo a2ensite etoolkit.conf

  * To disable site
  sudo a2dissite etoolkit.conf

  * Restart the Apache webserver to apply the changes:
  sudo systemctl reload apache2
  sudo systemctl restart apache2

  * List all the enabled sites**
  ls -l /etc/apache2/sites-enabled

  * Test the apache configuration:**
  sudo apachectl configtest

  * Install certbot in Ubuntu (enable ssl certificate)
  sudo apt install certbot python3-certbot-apache

  * Set SSL and enable https**
  sudo certbot --apache -d etoolkit.terrawatch.net
```

### Manual certificates (Optional)

Place certificate and key files, then in your Apache HTTPS virtual host set:

- `SSLCertificateFile` / path to certificate
- `SSLCertificateKeyFile` / path to private key
- `SSLCertificateChainFile` if required

Then reload Apache.



---
## 5. Screen Usage (for Background Jobs)
```
* Check the running screen 
screen doc: https://linuxize.com/post/how-to-use-linux-screen/
screen -r

* Start a new screen
screen -S etoolkit_server

* close the current runnign django_server and celery_worker screen
To attach a screen : 
screen -r 392898.etoolkit_server
screen -r 393313.etoolkit_celery

Then control+ C =

* Detach a screen
screen -d 404581.etoolkit_server

* To delete a screen 
screen -S 356415.etoolkit_server -X quit

```


## 6. Service Management

### Start / stop / restart

```bash
sudo systemctl start etoolkit_uwsgi etoolkit_celery apache2
sudo systemctl stop etoolkit_uwsgi etoolkit_celery apache2
sudo systemctl restart etoolkit_uwsgi etoolkit_celery apache2
```



### Status

```bash
sudo systemctl status etoolkit_uwsgi
sudo systemctl status etoolkit_celery
sudo systemctl status apache2
```

---

## 7. Logs

- Application: `tail -f /path/to/etoolkit/log/etoolkit.log`
- Celery: `tail -f /path/to/etoolkit/log/celery/worker1.log`
- Apache: `sudo tail -f /var/log/apache2/etoolkit_error.log` and `etoolkit_access.log`

---

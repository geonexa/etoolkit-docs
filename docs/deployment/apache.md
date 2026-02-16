# Apache Configuration

Guide for configuring Apache web server for EO-Toolkit.

## Prerequisites

- Apache installed
- uWSGI module installed
- SSL module installed (for HTTPS)

## Installation

```bash
# Install Apache
sudo apt-get install apache2

# Install uWSGI module
sudo apt install libapache2-mod-uwsgi

# Enable modules
sudo a2enmod uwsgi
sudo a2enmod ssl
```

## Configuration File

### Template Location

Template Apache configuration: `etoolkit/template_apache.conf`

### Copy and Modify

```bash
sudo cp etoolkit/template_apache.conf /etc/apache2/sites-available/etoolkit.conf
```

### Edit Configuration

```bash
sudo nano /etc/apache2/sites-available/etoolkit.conf
```

### Example Configuration

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
    
    ErrorLog ${APACHE_LOG_DIR}/etoolkit_error.log
    CustomLog ${APACHE_LOG_DIR}/etoolkit_access.log combined
</VirtualHost>
```

## Enable Site

```bash
# Enable site
sudo a2ensite etoolkit.conf

# Disable default site (optional)
sudo a2dissite 000-default.conf

# Test configuration
sudo apachectl configtest

# Reload Apache
sudo systemctl reload apache2
```

## SSL Configuration

### Using Let's Encrypt

```bash
# Install Certbot
sudo apt install certbot python3-certbot-apache

# Obtain certificate
sudo certbot --apache -d etoolkit.terrawatch.net

# Auto-renewal (already configured)
sudo certbot renew --dry-run
```

### Manual SSL

1. Obtain SSL certificate
2. Place certificates in secure location
3. Update Apache configuration
4. Restart Apache

## Static Files

### Collect Static Files

```bash
cd /path/to/etoolkit
source webapp/venv/bin/activate
python manage.py collectstatic --noinput
```

### Verify Static Files

- Check `/static/` URL serves files
- Verify file permissions
- Check Apache can read files

## Media Files

### Directory Setup

```bash
# Create media directory
mkdir -p /path/to/etoolkit/media

# Set permissions
chmod -R 755 /path/to/etoolkit/media
chown -R www-data:www-data /path/to/etoolkit/media
```

## Troubleshooting

### Configuration Test

```bash
sudo apachectl configtest
```

### Check Apache Status

```bash
sudo systemctl status apache2
```

### View Error Logs

```bash
sudo tail -f /var/log/apache2/etoolkit_error.log
```

### Common Issues

**Permission Denied**:
```bash
sudo chown -R www-data:www-data /path/to/etoolkit
sudo chmod -R 755 /path/to/etoolkit
```

**Module Not Found**:
```bash
sudo a2enmod uwsgi
sudo systemctl restart apache2
```

**SSL Issues**:
- Verify certificate paths
- Check certificate validity
- Verify SSL module enabled

## Performance Tuning

### Enable Compression

```apache
LoadModule deflate_module modules/mod_deflate.so

<Location />
    SetOutputFilter DEFLATE
</Location>
```

### Enable Caching

```apache
<Location /static>
    ExpiresActive On
    ExpiresDefault "access plus 1 year"
</Location>
```

## Security Headers

```apache
Header always set X-Content-Type-Options "nosniff"
Header always set X-Frame-Options "SAMEORIGIN"
Header always set X-XSS-Protection "1; mode=block"
```

---

Next: [uWSGI Setup](uwsgi.md)

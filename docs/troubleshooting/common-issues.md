# Common Issues and Solutions

This guide covers common issues when installing, running, or using EO-Toolkit and how to resolve them.

## Installation Issues

### Database connection failed

**Problem**: Cannot connect to PostgreSQL database.

**Solutions**:

- Verify PostgreSQL is running: `sudo systemctl status postgresql`
- Check database credentials in `.env` (POSTGRES_DB, POSTGRES_USER, POSTGRES_PASSWORD, POSTGRES_HOST, POSTGRES_PORT)
- Confirm the database exists: `psql -U etoolkit -l`
- Check PostgreSQL is listening: `sudo netstat -tlnp | grep 5432`

### GDAL installation issues

**Problem**: GDAL-related errors during setup or when loading spatial data.

**Solutions**:

- Verify GDAL is installed: `gdalinfo --version`
- Install development packages: `sudo apt-get install gdal-bin libgdal-dev`
- Reinstall Python GDAL bindings inside your virtualenv if needed

### Permission errors

**Problem**: Permission denied when running commands or when the web server accesses files.

**Solutions**:

```bash
# Fix ownership (replace with your deploy user if different)
sudo chown -R www-data:www-data /path/to/etoolkit

# Fix permissions
sudo chmod -R 755 /path/to/etoolkit

# Media directory
sudo chown -R www-data:www-data /path/to/etoolkit/media
```


---

## Runtime Issues

### Static files not loading

**Problem**: CSS or JavaScript files do not load; pages look unstyled.

**Solutions**:

- Collect static files: `python manage.py collectstatic --noinput`
- Check STATIC_ROOT and STATIC_URL in settings
- Verify Apache (or your web server) is configured to serve the static directory
- Check file permissions on the static root directory

### Media files not accessible

**Problem**: Uploaded files or thumbnails return 404 or permission errors.

**Solutions**:

- Confirm MEDIA_ROOT and MEDIA_URL in settings
- Ensure the media directory exists and has correct permissions
- Check web server configuration for the media alias or path
- In development, ensure `DEBUG=True` and static/media URLs are included in urlpatterns if needed

### GeoServer connection failed

**Problem**: Cannot connect to GeoServer or layers do not load.

**Solutions**:

- Verify GeoServer is running
- Check GEOSERVER_URL, GEOSERVER_USER, and GEOSERVER_PASSWORD in `.env`

---

## User and Application Issues

### Cannot log in

**Problem**: Login fails with invalid credentials or similar.

**Solutions**:

- Verify username and password
- Ensure the account is active (email verification completed)
- Check for account lockout or rate limiting
- Clear browser cookies and try again
- Use password reset if needed

### Email verification not working

**Problem**: OTP email not received after registration.

**Solutions**:

- Check spam or junk folder
- Verify email configuration in `.env` (EMAIL_HOST_USER, EMAIL_HOST_PASSWORD, etc.)
- Check application or mail server logs
- Confirm the email address is correct and try resending OTP


---

## Deployment Issues

### uWSGI not starting

**Problem**: uWSGI service fails to start.

**Solutions**:

- Check paths in the uWSGI INI file and systemd unit
- Verify virtual environment path and that `wsgi.py` exists
- Check log file for the exact error
- Ensure the run user (e.g. www-data) has permission to the project and socket directory

### Apache configuration errors

**Problem**: Apache does not start or the site does not load.

**Solutions**:

- Test configuration: `sudo apachectl configtest`
- Check error log: `sudo tail -f /var/log/apache2/error.log`
- Ensure required modules are enabled: `sudo a2enmod uwsgi ssl`
- Verify virtual host and paths in the site configuration

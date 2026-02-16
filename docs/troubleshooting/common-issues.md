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

### Missing SECRET_KEY

**Problem**: Application fails to start with an error about SECRET_KEY.

**Solutions**:

- Add `SECRET_KEY` to your `.env` file
- Generate a value: `python -c "from django.core.management.utils import get_random_secret_key; print(get_random_secret_key())"`

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
- Test connection: `curl -u $GEOSERVER_USER:$GEOSERVER_PASSWORD http://localhost:8080/geoserver/rest/workspaces.json` (use credentials from your `.env`)

### Celery tasks not processing

**Problem**: Tasks are queued but never run (e.g. report generation).

**Solutions**:

- Check Celery workers are running: `sudo systemctl status etoolkit_celery` (or your service name)
- Verify Redis is running: `redis-cli ping` (should return PONG)
- Check Celery logs: `tail -f /path/to/etoolkit/log/celery/worker1.log`
- Restart Celery: `sudo systemctl restart etoolkit_celery`

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

### Area upload fails

**Problem**: GeoJSON upload for an area fails.

**Solutions**:

- Ensure the file is valid GeoJSON
- Use only Polygon or MultiPolygon geometry types
- Use EPSG:4326 (WGS84) or ensure the file can be converted
- Check file size limits
- Verify the file is not corrupted

---

## Performance Issues

### Slow page loads

**Problem**: Pages take a long time to load.

**Solutions**:

- Check server resources (CPU, memory, disk)
- Optimize database queries and add indexes if needed
- Enable caching where appropriate
- Verify network and reverse proxy configuration
- Review application and web server logs for slow requests

### Analysis takes too long

**Problem**: Analyses run for a very long time or time out.

**Solutions**:

- Reduce the area size
- Limit the temporal range (e.g. fewer years)
- Check Google Earth Engine quota or service status
- Verify server and Celery worker resources
- Avoid running too many heavy analyses at once

### High memory usage

**Problem**: Server runs out of memory.

**Solutions**:

- Reduce uWSGI worker processes or threads
- Reduce Celery concurrency
- Optimize GeoServer or application settings
- Add swap if appropriate
- Consider scaling to more memory or more nodes

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

### SSL certificate issues

**Problem**: HTTPS fails or browser reports certificate errors.

**Solutions**:

- Verify certificate file paths in Apache configuration
- Check certificate validity and expiration
- Ensure the SSL module is enabled and Apache reloaded
- For Let's Encrypt, run `sudo certbot renew --dry-run` to test renewal

---

## Data and Analysis Issues

### Dataset not loading on map

**Problem**: A dataset or layer does not appear on the map.

**Solutions**:

- Confirm the dataset exists and is published in GeoServer (for GeoServer layers)
- Test the WMS URL or layer in a separate client if possible
- Verify the area of interest is within the data coverage
- Check the browser console and network tab for failed requests

### Analysis returns no data

**Problem**: Analysis completes but shows no data or empty charts.

**Solutions**:

- Ensure the selected area is within the dataset coverage
- Check that the date range is valid for the dataset
- Verify the area geometry is valid
- Try a different area or dataset to isolate the issue

---

# Common Issues and Solutions

This guide covers common issues encountered when using EO-Toolkit and their solutions.

## Installation Issues

### Database Connection Failed

**Problem**: Cannot connect to PostgreSQL database

**Solutions**:
- Verify PostgreSQL is running: `sudo systemctl status postgresql`
- Check database credentials in settings
- Verify database exists: `psql -U etoolkit -l`
- Check PostgreSQL is listening: `sudo netstat -tlnp | grep 5432`

### GDAL Installation Issues

**Problem**: GDAL-related errors

**Solutions**:
- Verify GDAL installed: `gdalinfo --version`
- Install GDAL development packages: `sudo apt-get install gdal-bin libgdal-dev`
- Rebuild Python GDAL bindings if needed

### Permission Errors

**Problem**: Permission denied errors

**Solutions**:
```bash
# Fix ownership
sudo chown -R user:user /path/to/etoolkit

# Fix permissions
sudo chmod -R 755 /path/to/etoolkit

# For media directory
sudo chown -R www-data:www-data /path/to/etoolkit/media
```

## Runtime Issues

### Static Files Not Loading

**Problem**: CSS/JS files not loading

**Solutions**:
- Collect static files: `python manage.py collectstatic`
- Check STATIC_ROOT and STATIC_URL settings
- Verify Apache/Apache configuration
- Check file permissions

### Media Files Not Accessible

**Problem**: Uploaded files not accessible

**Solutions**:
- Check MEDIA_ROOT and MEDIA_URL settings
- Verify media directory exists and has permissions
- Check Apache configuration for media alias
- Verify file permissions

### GeoServer Connection Failed

**Problem**: Cannot connect to GeoServer

**Solutions**:
- Verify GeoServer is running
- Check GeoServer URL in settings
- Verify credentials
- Test connection: `curl -u user:pass http://geoserver:8080/geoserver/rest/workspaces.json`

### Celery Tasks Not Processing

**Problem**: Tasks queued but not executing

**Solutions**:
- Check Celery workers running: `sudo systemctl status etoolkit_celery`
- Verify Redis connection: `redis-cli ping`
- Check Celery logs: `tail -f /path/to/log/celery/worker1.log`
- Restart Celery: `sudo systemctl restart etoolkit_celery`

## User Issues

### Cannot Log In

**Problem**: Login fails

**Solutions**:
- Verify credentials
- Check account is active
- Verify email is verified
- Check for account lockout
- Clear browser cookies

### Email Verification Not Working

**Problem**: OTP email not received

**Solutions**:
- Check spam folder
- Verify email configuration
- Check email server logs
- Verify email address
- Try resending OTP

### Area Upload Fails

**Problem**: GeoJSON upload fails

**Solutions**:
- Verify file format (valid GeoJSON)
- Check geometry type (Polygon/MultiPolygon)
- Ensure EPSG:4326 coordinate system
- Check file size limits
- Verify file is not corrupted

## Performance Issues

### Slow Page Loads

**Problem**: Pages load slowly

**Solutions**:
- Check server resources (CPU, memory)
- Optimize database queries
- Enable caching
- Check network connectivity
- Review Apache/uWSGI configuration

### Analysis Takes Too Long

**Problem**: Analyses run very slowly

**Solutions**:
- Reduce area size
- Limit temporal range
- Check GEE quota limits
- Verify server resources
- Check for concurrent analyses

### High Memory Usage

**Problem**: Server running out of memory

**Solutions**:
- Reduce uWSGI workers
- Reduce Celery concurrency
- Optimize GeoServer configuration
- Add swap space
- Consider server upgrade

## Deployment Issues

### uWSGI Not Starting

**Problem**: uWSGI service fails to start

**Solutions**:
- Check configuration file paths
- Verify virtual environment path
- Check log files for errors
- Verify user permissions
- Check socket file permissions

### Apache Configuration Errors

**Problem**: Apache won't start or serve site

**Solutions**:
- Test configuration: `sudo apachectl configtest`
- Check error logs: `sudo tail -f /var/log/apache2/error.log`
- Verify modules enabled: `sudo a2enmod uwsgi ssl`
- Check virtual host configuration

### SSL Certificate Issues

**Problem**: SSL certificate errors

**Solutions**:
- Verify certificate paths
- Check certificate validity
- Renew expired certificates
- Verify certificate chain
- Check Apache SSL configuration

## Data Issues

### Dataset Not Loading

**Problem**: Dataset fails to load on map

**Solutions**:
- Verify dataset exists
- Check GeoServer layer published
- Test WMS URL directly
- Verify area within data coverage
- Check browser console for errors

### Analysis Returns No Data

**Problem**: Analysis completes but shows no data

**Solutions**:
- Verify area within data coverage
- Check date range validity
- Ensure area geometry is valid
- Try different area
- Check data availability for region

## Getting Help

If issues persist:

1. Check logs for detailed error messages
2. Review relevant documentation sections
3. Search for similar issues
4. Contact support with:
   - Error messages
   - Steps to reproduce
   - System information
   - Relevant log excerpts

---

Next: [Error Messages](errors.md)

# Deployment Overview

This guide covers deploying EO-Toolkit to a production environment.

## Deployment Architecture

EO-Toolkit production deployment uses:

- **Apache**: Web server
- **uWSGI**: WSGI application server
- **Celery**: Asynchronous task queue
- **Redis**: Message broker for Celery
- **PostgreSQL**: Database with PostGIS
- **GeoServer**: Spatial data server

## Prerequisites

- Ubuntu Server (18.04+)
- All system dependencies installed
- Database configured
- Application code deployed
- SSL certificate (for HTTPS)

## Deployment Steps

1. [Apache Setup](apache.md) - Configure web server
2. [uWSGI Setup](uwsgi.md) - Configure application server
3. [Celery Setup](celery.md) - Configure task queue
4. [SSL Configuration](ssl.md) - Enable HTTPS

## Quick Checklist

- [ ] System dependencies installed
- [ ] Database created and migrated
- [ ] Static files collected
- [ ] Media directories created
- [ ] Apache configured
- [ ] uWSGI configured
- [ ] Celery configured
- [ ] SSL certificate installed
- [ ] Services started and enabled
- [ ] Firewall configured
- [ ] Monitoring set up

## Service Management

### Starting Services

```bash
sudo systemctl start etoolkit_uwsgi
sudo systemctl start etoolkit_celery
sudo systemctl start apache2
```

### Enabling on Boot

```bash
sudo systemctl enable etoolkit_uwsgi
sudo systemctl enable etoolkit_celery
sudo systemctl enable apache2
```

### Checking Status

```bash
sudo systemctl status etoolkit_uwsgi
sudo systemctl status etoolkit_celery
sudo systemctl status apache2
```

## Logs

### Application Logs

```bash
tail -f /path/to/etoolkit/log/etoolkit.log
```

### Celery Logs

```bash
tail -f /path/to/etoolkit/log/celery/worker1.log
```

### Apache Logs

```bash
sudo tail -f /var/log/apache2/etoolkit_error.log
sudo tail -f /var/log/apache2/etoolkit_access.log
```

## Security Considerations

1. **HTTPS**: Always use HTTPS in production
2. **Firewall**: Configure firewall rules
3. **Secrets**: Keep secrets in environment variables
4. **Updates**: Keep system and dependencies updated
5. **Backups**: Regular database and file backups
6. **Monitoring**: Set up monitoring and alerts

## Performance Optimization

1. **Static Files**: Serve via Apache/CDN
2. **Caching**: Enable caching where appropriate
3. **Database**: Optimize database queries
4. **Celery**: Configure appropriate worker count
5. **GeoServer**: Optimize GeoServer configuration

## Monitoring

### Health Checks

- Application response time
- Celery worker status
- Database connection
- Disk space
- Memory usage

### Alerts

Set up alerts for:
- Service failures
- High error rates
- Disk space low
- High memory usage
- SSL certificate expiration

---

Next: [Apache Setup](apache.md)

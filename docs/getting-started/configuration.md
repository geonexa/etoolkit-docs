# Configuration Guide

This guide covers the essential configuration steps for EO-Toolkit after installation.

## Environment Variables

EO-Toolkit uses environment variables for sensitive configuration. Create a `.env` file in the project root:

```bash
cd /path/to/etoolkit
nano .env
```

### Required Environment Variables

```env
# Django Settings
DJANGO_ENV=development
SECRET_KEY=your-secret-key-here
BASE_URL=http://localhost:8000

# Database Configuration
DB_NAME=etoolkit
DB_USER=etoolkit
DB_PASSWORD=etoolkit123
DB_HOST=localhost
DB_PORT=5432

# GeoServer Configuration
GEOSERVER_URL=http://localhost:8080/geoserver
GEOSERVER_WMS_URL=http://localhost:8080/geoserver
GEOSERVER_USER=admin
GEOSERVER_PASSWORD=geoserver
GEOSERVER_WORKSPACE=etoolkit

# Email Configuration (for OTP and notifications)
EMAIL_HOST=smtp.gmail.com
EMAIL_PORT=587
EMAIL_HOST_USER=your-email@gmail.com
EMAIL_HOST_PASSWORD=your-app-password
EMAIL_USE_TLS=True

# Google Earth Engine (if using)
EE_KEY={"type":"service_account",...}
```

!!! warning "Security"
    Never commit the `.env` file to version control. It contains sensitive information.

## Django Settings Configuration

### Database Settings

Edit `etoolkit/settings.py`:

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.contrib.gis.db.backends.postgis',
        'NAME': config('DB_NAME', default='etoolkit'),
        'USER': config('DB_USER', default='etoolkit'),
        'PASSWORD': config('DB_PASSWORD'),
        'HOST': config('DB_HOST', default='localhost'),
        'PORT': config('DB_PORT', default='5432'),
    }
}
```

### Static and Media Files

```python
STATIC_URL = '/static/'
STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')

MEDIA_URL = '/media/'
MEDIA_ROOT = os.path.join(BASE_DIR, 'media')
```

### Allowed Hosts

For production, update `ALLOWED_HOSTS`:

```python
ALLOWED_HOSTS = ['yourdomain.com', 'www.yourdomain.com']
```

### CSRF Trusted Origins

```python
CSRF_TRUSTED_ORIGINS = ['https://yourdomain.com']
```

## GeoServer Configuration

### Install GeoServer

1. Download GeoServer from [geoserver.org](https://geoserver.org/download/)
2. Extract and configure
3. Start GeoServer service

### Create Workspace

1. Log into GeoServer admin panel (usually `http://localhost:8080/geoserver`)
2. Navigate to **Workspaces** → **Add new workspace**
3. Create workspace named `etoolkit` (or your configured workspace name)

### Configure Data Directory

Ensure GeoServer has access to your data directory:

```bash
# Create data directory
mkdir -p /path/to/etoolkit/media/geoserver_data

# Set permissions
chmod -R 755 /path/to/etoolkit/media/geoserver_data
```

## Redis Configuration

Redis is used for Celery task queue. Ensure Redis is running:

```bash
# Check Redis status
sudo systemctl status redis

# Start Redis if not running
sudo systemctl start redis

# Enable Redis on boot
sudo systemctl enable redis
```

## Celery Configuration

Celery configuration is in `etoolkit/celery.py`. Key settings:

```python
broker_url = 'redis://localhost:6379/0'
result_backend = 'django-db'
```

## Email Configuration

For email verification and notifications:

```python
EMAIL_BACKEND = 'django.core.mail.backends.smtp.EmailBackend'
EMAIL_HOST = config('EMAIL_HOST')
EMAIL_PORT = config('EMAIL_PORT', cast=int)
EMAIL_USE_TLS = config('EMAIL_USE_TLS', cast=bool, default=True)
EMAIL_HOST_USER = config('EMAIL_HOST_USER')
EMAIL_HOST_PASSWORD = config('EMAIL_HOST_PASSWORD')
DEFAULT_FROM_EMAIL = config('EMAIL_HOST_USER')
```

!!! tip "Gmail Setup"
    For Gmail, you'll need to:
    1. Enable 2-factor authentication
    2. Generate an app-specific password
    3. Use the app password in `EMAIL_HOST_PASSWORD`

## Google Earth Engine Setup

If using Google Earth Engine features:

1. Create a Google Cloud Project
2. Enable Earth Engine API
3. Create a service account
4. Download the service account key JSON
5. Set `EE_KEY` environment variable with the JSON content

## Logging Configuration

Configure logging in `settings.py`:

```python
LOGGING = {
    'version': 1,
    'disable_existing_loggers': False,
    'handlers': {
        'file': {
            'level': 'INFO',
            'class': 'logging.FileHandler',
            'filename': os.path.join(BASE_DIR, 'log', 'etoolkit.log'),
        },
    },
    'loggers': {
        'django': {
            'handlers': ['file'],
            'level': 'INFO',
            'propagate': True,
        },
    },
}
```

## Verify Configuration

Test your configuration:

```bash
# Check Django settings
python manage.py check

# Test database connection
python manage.py dbshell

# Test Celery connection
celery -A etoolkit inspect active

# Test GeoServer connection
curl -u admin:geoserver http://localhost:8080/geoserver/rest/workspaces.json
```

## Next Steps

- [First Steps](first-steps.md) - Learn how to use the application
- [Deployment Guide](../deployment/overview.md) - Configure for production

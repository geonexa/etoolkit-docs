# Installation Guide

This guide will walk you through installing EO-Toolkit on a Ubuntu server system.

## System Requirements

### Operating System
- Ubuntu Server (18.04 or later recommended)

### Required Software

Before installing EO-Toolkit, ensure your system has the following software installed:

- PostgreSQL and its developer packages
- PostGIS (spatial database extension)
- Redis (for Celery task queue)
- Git
- GDAL (Geospatial Data Abstraction Library)
- Apache web server
- Build-essential (compilers)
- Python 3 development packages

## Step 1: Install System Dependencies

Open a terminal and run the following commands:

```bash
# Update package list
sudo apt-get update

# Install required libraries
sudo apt-get install -y gdal-bin apache2 postgis redis-server \
    build-essential python3-dev libpq-dev pango1.0-tools

# Install PostgreSQL with PostGIS
sudo apt-get install -y postgresql postgresql-postgis

# Install uWSGI module for Apache
sudo apt install -y libapache2-mod-uwsgi
```

## Step 2: Database Setup

### Create PostgreSQL User and Database

```bash
# Create a PostgreSQL user
sudo -u postgres createuser etoolkit

# Open PostgreSQL command line
sudo -u postgres psql
```

In the PostgreSQL command line (`psql`), execute:

```sql
-- Change password of postgres user
ALTER USER postgres PASSWORD 'etoolkit';

-- Set password for etoolkit user
ALTER USER etoolkit PASSWORD 'etoolkit123';

-- Grant superuser privileges (for development)
ALTER USER etoolkit WITH SUPERUSER;

-- Exit psql
\q
```

### Create Database with PostGIS Extension

```bash
# Create the database
createdb -U etoolkit -h localhost etoolkit

# Enable PostGIS extension
psql -U etoolkit -h localhost etoolkit -c "CREATE EXTENSION postgis"
```

!!! note "Database Credentials"
    Remember to change the default passwords in production environments!

## Step 3: Clone the Repository

```bash
# Navigate to your desired installation directory
cd /home/yourusername/

# Clone the repository
git clone <repository-url> etoolkit
cd etoolkit
```

## Step 4: Python Virtual Environment Setup

```bash
# Navigate to the webapp directory
cd etoolkit/webapp

# Create a Python 3 virtual environment
python3 -m venv venv

# Activate the virtual environment
source venv/bin/activate
```

## Step 5: Install Python Dependencies

```bash
# Upgrade pip
pip install --upgrade pip

# Install dependencies
pip install -r requirements.txt
```

## Step 6: Configure Django Settings

Edit the Django settings file to configure database connection and other settings:

```bash
# Edit settings.py
nano etoolkit/settings.py
```

Update the following settings:

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.contrib.gis.db.backends.postgis',
        'NAME': 'etoolkit',
        'USER': 'etoolkit',
        'PASSWORD': 'etoolkit123',
        'HOST': 'localhost',
        'PORT': '5432',
    }
}
```

Also configure:
- `GEOSERVER_URL`
- `GEOSERVER_WMS_URL`
- `GEOSERVER_USER`
- `GEOSERVER_PASSWORD`
- `GEOSERVER_WORKSPACE`
- `SECRET_KEY` (generate a new one for production)

## Step 7: Initialize Database

```bash
# Create database migrations
python manage.py makemigrations webapp

# Apply migrations
python manage.py migrate

# Collect static files
python manage.py collectstatic
```

## Step 8: Create Superuser

Create an admin user to access the Django admin panel:

```bash
python manage.py createsuperuser --username admin
```

Follow the prompts to set email and password.

## Step 9: Verify Installation

### Development Mode Testing

```bash
# Start Celery worker (in a separate terminal)
celery -A etoolkit worker -l INFO

# Start Django development server
python manage.py runserver

# Or run on a specific port
python manage.py runserver 127.0.0.1:8002
```

Open your web browser and navigate to:
- `http://127.0.0.1:8000/` (or your configured port)
- `http://server_ip:8002/` (for access from other devices on the network)

## Next Steps

- [Configure your environment](configuration.md)
- [Set up for production deployment](../deployment/overview.md)
- [Learn the basics](../getting-started/first-steps.md)

## Troubleshooting

If you encounter issues during installation:

1. **Permission Errors**: Ensure your user has proper permissions for the project directory
2. **Database Connection**: Verify PostgreSQL is running: `sudo systemctl status postgresql`
3. **GDAL Issues**: Check GDAL installation: `gdalinfo --version`
4. **Python Packages**: Ensure virtual environment is activated before installing packages

For more help, see the [Troubleshooting](../troubleshooting/common-issues.md) section.

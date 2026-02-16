# Installation Guide

This guide walks you through installing, configuring, and getting started with EO-Toolkit on an Ubuntu server.

## System Requirements

### Hardware Specifications

- **Server Type**: Linux-based Ubuntu server
- **Processor**: AMD or Intel processor with minimum 32 cores
- **RAM**: Minimum 126 GB for efficient performance
- **Storage**: Minimum 5 TB (considering future expansion of the database)
- **Access**: Full admin access to the server is required

### Operating System

- Ubuntu Server 22.04 (required)

### Required Software

- PostgreSQL and its developer packages
- PostGIS (spatial database extension)
- Redis (for Celery task queue)
- Git
- GDAL (Geospatial Data Abstraction Library)
- Apache web server
- Build-essential (compilers)
- Python 3 development packages

---

## Step 1: Install System Dependencies

```bash
sudo apt-get update
sudo apt-get install -y gdal-bin apache2 postgis redis-server \
    build-essential python3-dev libpq-dev pango1.0-tools
sudo apt-get install -y postgresql postgresql-postgis
sudo apt install -y libapache2-mod-uwsgi
```

## Step 2: Database Setup

Create a PostgreSQL user and database:

```bash
sudo -u postgres createuser etoolkit
sudo -u postgres psql
```

In `psql` (use strong passwords of your choice):

```sql
ALTER USER postgres PASSWORD 'etoolkit';
ALTER USER etoolkit PASSWORD 'etoolkit123';
ALTER USER etoolkit WITH SUPERUSER;
\q
```

Create the database and enable PostGIS:

```bash
createdb -U etoolkit -h localhost etoolkit
psql -U etoolkit -h localhost etoolkit -c "CREATE EXTENSION postgis"
```

## Step 3: Clone the Repository

```bash
cd /home/yourusername/
git clone <repository-url> etoolkit
cd etoolkit
```

## Step 4: Python Virtual Environment

```bash
cd etoolkit/webapp
python3 -m venv venv
source venv/bin/activate
```

## Step 5: Install Python Dependencies

```bash
pip install --upgrade pip
pip install -r requirements.txt
```

---

## Step 6: Environment and Configuration

### Create the environment file

Copy the example file and edit it (path is inside the `etoolkit` project directory):

```bash
cd /path/to/etoolkit/etoolkit
cp .env.example .env
nano .env
```

### Environment file template

Use this template (based on `.env.example`). Set values for your environment.

```env
OPENAI_API_KEY=
EE_KEY=


DJANGO_ENV=development
DATABASE_NAME=db.sqlite3

# For production, uncomment and set:
# DJANGO_ENV=production
# POSTGRES_DB=
# POSTGRES_USER=
# POSTGRES_PASSWORD=
# POSTGRES_HOST=localhost
# POSTGRES_PORT=5432


ADMIN_EMAIL=
EMAIL_HOST_USER=
EMAIL_HOST_PASSWORD=


GEOSERVER_URL=
GEOSERVER_WMS_URL=
GEOSERVER_USER=
GEOSERVER_PASSWORD=
GEOSERVER_WORKSPACE=
```



### How to obtain API keys

**OPENAI_API_KEY** (optional; used for AI features):

1. Go to [OpenAI Platform](https://platform.openai.com/) and sign in.
2. Open **API keys** and click **Create new secret key**.
3. Copy the key and set in `.env`: `OPENAI_API_KEY=sk-proj-...`

**EE_KEY** (Google Earth Engine; required for GEE analyses):

1. In [Google Cloud Console](https://console.cloud.google.com/), create or select a project.
2. Enable **Google Earth Engine API** (APIs & Services → Library).
3. Create a **Service account** (APIs & Services → Credentials → Create credentials → Service account).
4. Request Earth Engine access at [signup.earthengine.google.com](https://signup.earthengine.google.com/) and add the service account email as a user.
5. For the service account, add a key: **Keys** → **Add key** → **Create new key** → **JSON**, and download the file.
6. Set `EE_KEY` in `.env` to the **entire JSON object** as a single line (e.g. `EE_KEY={"type":"service_account",...}`). Ensure the value is correctly quoted; escape internal double quotes if required by your env loader.

### GeoServer configuration

1. Install GeoServer from [geoserver.org](https://geoserver.org/download/) and start it.
2. In the GeoServer admin UI, create a workspace (e.g. `etoolkit`).
3. Set `GEOSERVER_URL`, `GEOSERVER_WMS_URL`, `GEOSERVER_USER`, `GEOSERVER_PASSWORD`, and `GEOSERVER_WORKSPACE` in `.env` to match your setup.
4. Create and permit the data directory:
   ```bash
   mkdir -p /path/to/etoolkit/media/geoserver_data
   chmod -R 755 /path/to/etoolkit/media/geoserver_data
   ```

### Email configuration

Set `ADMIN_EMAIL`, `EMAIL_HOST_USER`, and `EMAIL_HOST_PASSWORD` in `.env` for OTP and notifications. For Gmail: enable 2-factor authentication, create an app password, and use it in `EMAIL_HOST_PASSWORD`.

---

## Step 7: Initialize Database

```bash
cd /path/to/etoolkit
python manage.py makemigrations webapp
python manage.py migrate
python manage.py collectstatic
```

## Step 8: Create Superuser

```bash
python manage.py createsuperuser --username admin
```

Set email and password when prompted.

## Step 9: Testing (Development Mode)

Start Celery (in a separate terminal) and the Django server:

```bash
* Start celery worker to use asynchronous requests
celery -A etoolkit worker -l INFO

* At this point you could run the app
python3 manage.py runserver

* run this to access on other device too
python manage.py runserver 0.0.0.0:8002


```

Open in a browser:

- `http://127.0.0.1:8000/` (or your port)
- From other devices: `http://server_ip:8002/`

---

## First Steps: Using the Application

### Accessing the application

1. Open your browser and go to your EO-Toolkit URL (e.g. `http://localhost:8000` or `https://etoolkit.terrawatch.net`).

### Creating your account

1. Click **Sign Up** or go to `/accounts/register/`.
2. Enter username, email, password, and confirm password, then click **Register**.
3. On the email verification page, click **Send OTP**, check your email for the 6-digit code, enter it, and click **Verify**.
4. Go to `/accounts/login/`, enter username and password, and click **Sign In**.


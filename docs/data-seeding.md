# Data Seeding and Migration

This page covers **seeding** (loading initial catalog and GeoServer data from JSON) and **migration** (restoring a developer-provided **export package** on a new server).

EO-Toolkit uses multiple catalogs: Data Catalog, Case Studies, Training Material, Partners Catalog, and others. Catalog data is provided in JSON under `seed/staticData`, sourced from curated datasets and updated over time. After database setup, you can populate the application either by **seeding** from these JSON files or by **restoring** a database dump provided by a developer.

---

## When to use what

| Scenario | Use |
|----------|-----|
| **Fresh install** – empty database, first time | Run [Django migrations](installation.md#step-7-initialize-database), then [Seeding catalog data](#seeding-catalog-data) and optionally [Bulk GeoServer data seeding](#bulk-geoserver-data-seeding). |
| **Migration** – developer gave you an export package (`etoolkit_export_YYYYMMDD`) | Follow [Migration using the export package](#migration-using-the-export-package). |
| **Adding more catalog or GeoServer data** | Use the same [seed commands](#seeding-catalog-data) and [GeoServer seeding](#bulk-geoserver-data-seeding) as needed. |

---

## Migration using the export package

When a developer provides an existing PostgreSQL/PostGIS dump (e.g. from a previous EO-Toolkit instance), you can use it on a **new server** so the new setup starts with that data instead of seeding from JSON.

### Prerequisites

- New server with PostgreSQL and PostGIS installed (see [Installation Guide](installation.md)).
- The dump file from the developer (e.g. `etoolkit_dump.sql` or `etoolkit_dump.dump`).
- EO-Toolkit application code and virtualenv set up on the new server; `.env` not yet pointing at the restored DB (or you will point it in these steps).
- GeoServer reachable; you will create workspace **`etoolkit`** (or reuse an empty one)

### Step 0: Unpack and verify the package

```bash
# If you received a single archive:
tar -xzvf etoolkit_export_YYYYMMDD.tar.gz
cd etoolkit_export_YYYYMMDD

# Inspect
ls database media manifest
cat manifest/README.txt
cat manifest/sizes.txt

# Checksums: SHA256SUMS may list absolute paths from the source host.
# Re-hash locally and compare to the hash values in SHA256SUMS:
sha256sum database/*.dump media/*.tar.gz
cat manifest/SHA256SUMS
```

### Step 1: Create database and enable PostGIS

```bash
sudo -u postgres createuser -P etoolkit
createdb -U etoolkit -h localhost etoolkit
psql -U etoolkit -h localhost etoolkit -c "CREATE EXTENSION IF NOT EXISTS postgis;"
```

Use the same DB name/user/password you will set in `.env`.

### Step 2: Restore the database dump

Prefer the **custom-format** `.dump` file:

```bash
pg_restore -U etoolkit -h localhost -d etoolkit --no-owner --no-acl \
  /path/to/etoolkit_export_YYYYMMDD/database/etoolkit_YYYYMMDD.dump
```

Alternatively, plain SQL:

```bash
# gunzip -k etoolkit_YYYYMMDD.sql.gz   # if you only have .gz
psql -U etoolkit -h localhost etoolkit \
  -f /path/to/etoolkit_export_YYYYMMDD/database/etoolkit_YYYYMMDD.sql
```

- `--no-owner --no-acl` avoids permission errors from the source server.
- Non-fatal “already exists” warnings can appear; investigate only if restore fails overall.

### Step 3: Restore the full `media/` folder

The dump references files under Django `MEDIA_ROOT` (`etoolkit/media/`). Unpack the media archive into the project so paths match:

```bash
# Django dir = directory that contains settings and the media/ folder
export DJANGO_DIR=/path/to/etoolkit/etoolkit

tar -C "$DJANGO_DIR" -xzvf /path/to/etoolkit_export_YYYYMMDD/media/media_YYYYMMDD.tar.gz

# Expect:
#   $DJANGO_DIR/media/geoserver_data/
#   $DJANGO_DIR/media/reports/
#   $DJANGO_DIR/media/custom_app/
ls "$DJANGO_DIR/media"
du -sh "$DJANGO_DIR/media"/*
```

Ensure the application user can read/write:

```bash
chmod -R u+rwX "$DJANGO_DIR/media"
```

### Step 4: Configure `.env` on the new server

```env
DJANGO_ENV=production
POSTGRES_DB=etoolkit
POSTGRES_USER=etoolkit
POSTGRES_PASSWORD=your_password
POSTGRES_HOST=localhost
POSTGRES_PORT=5432

GEOSERVER_URL=https://your-geoserver-host/geoserver
GEOSERVER_WMS_URL=https://your-geoserver-host/geoserver
GEOSERVER_USER=admin
GEOSERVER_PASSWORD=your_geoserver_password
GEOSERVER_WORKSPACE=etoolkit
```

Also set `OPENAI_API_KEY`, `EE_KEY`, email settings as required. 

### Step 5: GeoServer workspace and layers

Because the export does **not** include a full shared GeoServer `data_dir`:

1. Install/start GeoServer (or use an existing instance).
2. Create workspace **`etoolkit`** (empty is fine).
3. Point `.env` at that GeoServer (Step 4).
4. Re-publish eToolkit layers from restored `media/geoserver_data/`:

```bash
cd /path/to/etoolkit
source venv/bin/activate
python manage.py seed --only geoserver_data
```

If layer metadata is already in the restored DB, you may re-upload or trigger bulk upload from Django Admin (**Geo Data Uploads**) instead. Files must come from restored `media/geoserver_data/` — not from other tools’ GeoServer workspaces.

### Step 6: Run migrations and collect static

```bash
cd /path/to/etoolkit
source venv/bin/activate
python manage.py migrate
python manage.py collectstatic --noinput
```

### Step 7: Superuser (if needed)

The dump usually includes existing users. Create another admin only if required:

```bash
python manage.py createsuperuser --username admin
```

### Step 8: Post-migration smoke tests

Treat any failure as a blocker until investigated.

#### Database and schema

```bash
python manage.py showmigrations
python manage.py check
psql -U etoolkit -h localhost etoolkit -c "SELECT PostGIS_Version();"
```

#### Core data and media

```bash
psql -U etoolkit -h localhost etoolkit -c "
SELECT (SELECT COUNT(*) FROM django_migrations) AS migrations_applied;
"
psql -U etoolkit -h localhost etoolkit -c "\dt *user*"

ls /path/to/etoolkit/etoolkit/media/geoserver_data | head
ls /path/to/etoolkit/etoolkit/media/reports | head
```

Confirm in Django Admin: catalogs, Geo Data Uploads, and that report/media links resolve.

#### Application endpoints

| Check | Expected |
|-------|----------|
| Home page | HTTP 200 |
| `/admin/` login | Works with a user from the dump (or new superuser) |
| Catalog / map pages | No 500 errors |
| WMS / map layer | Tiles load from GeoServer workspace `etoolkit` |
| One analysis/report (if used) | Celery picks up the task |

```bash
curl -I http://127.0.0.1:8000/
curl -I http://127.0.0.1:8000/admin/
```

#### Services

```bash
sudo systemctl status etoolkit_uwsgi
sudo systemctl status etoolkit_celery
sudo systemctl status apache2
```

See [Logs Management](administration/logs-management.md) if something fails.

### Migration checklist (export package)

1. Verify package (`manifest/SHA256SUMS`, sizes).
2. Create DB + enable PostGIS.
3. Restore `database/*.dump` (or `.sql`).
4. Unpack `media/media_*.tar.gz` into Django `media/`.
5. Configure `.env` (DB + GeoServer + API keys).
6. Create GeoServer workspace `etoolkit` and publish layers from `media/geoserver_data/`.
7. `migrate` + `collectstatic`.
8. Superuser if needed.
9. [Post-migration smoke tests](#step-8-post-migration-smoke-tests).

---

## Seeding catalog data

Catalog data (countries, case studies, training material, partners, etc.) is loaded from JSON files via a Django management command. Use this for a **fresh** database or to add/refresh catalog content.

**Management command:** `etoolkit/webapp/management/commands/seed.py`  
**JSON seed files:** `etoolkit/webapp/seed/staticData/`

### Key commands

Run all registered seeds (recommended after migrations on a fresh DB):

```bash
python manage.py seed
```

Quieter output:

```bash
python manage.py seed --quiet
```

### Run individual seeds

| Seed | Command |
|------|---------|
| Countries | `python manage.py seed --only countries` |
| Case studies | `python manage.py seed --only case_studies` |
| Training material | `python manage.py seed --only training_material` |
| Data recommendations | `python manage.py seed --only data_recommendation` |
| GEE datasets | `python manage.py seed --only gee_datasets` |
| SOP items | `python manage.py seed --only sops` |
| Partners catalog | `python manage.py seed --only partners` |
| GeoServer catalog + upload | `python manage.py seed --only geoserver_data` |

**Examples:**

```bash
python manage.py seed --only countries
python manage.py seed --only case_studies
python manage.py seed --only training_material
python manage.py seed --only data_recommendation
python manage.py seed --only gee_datasets
python manage.py seed --only sops
python manage.py seed --only partners
python manage.py seed --only geoserver_data
```

---

## Bulk GeoServer data seeding

This loads raster/vector datasets and their styling into GeoServer from a structured folder layout and a metadata JSON file.

### 1. Prepare data files

Place each dataset under:

```
/media/geoserver_data/<folder_name>/
```

Per dataset, provide:

- **GeoTIFF or Shapefile ZIP** – raster or vector data
- **.sld file** – styling
- **Thumbnail image** – e.g. PNG

**Example:**

```
media/geoserver_data/Niger_LULC/
├── Niger_LULC.tif
├── Niger_LULC.sld
├── Niger_LULC.png
```

After a migration, this tree already exists inside the restored `media/` archive.

### 2. Bulk metadata JSON

- Path (typical): under `etoolkit/webapp/seed/` (e.g. `staticData/` or related seed data), including `geoserver_data.json`
- Each entry describes one dataset (name, files, provider, etc.).

### 3. What the seed command does

- Reads and parses `geoserver_data.json`.
- For each entry:
  - Checks if a dataset with the same name already exists
  - Validates file paths under `MEDIA_ROOT/geoserver_data`
  - Creates a `GeoDataUpload` record and uploads data and style to GeoServer

### 4. Trigger GeoServer seeding

```bash
python manage.py seed --only geoserver_data
```

---

## Quick reference

- **Fresh install:** migrations → [Seeding catalog data](#seeding-catalog-data) → [Bulk GeoServer data seeding](#bulk-geoserver-data-seeding) (optional) → smoke checks.
- **Migration with export package:** [Migration using the export package](#migration-using-the-export-package) → DB restore + media unpack + GeoServer `etoolkit` workspace + smoke tests.
- **Full production runbook (start → go-live):** [Production Migration Checklist](production-migration-checklist.md).
- **Refresh catalog/layers only:** [seed commands](#run-individual-seeds) / [GeoServer seeding](#bulk-geoserver-data-seeding).

# Production Migration Checklist

Checklist for migrating EO-Toolkit to a **new production server**. Work through the phases in order.

**Primary path:** restore a developer **export package** (`etoolkit_export_YYYYMMDD`) — database dump + full `media/` — then deploy the app stack.

| Related guide | URL / page |
|---------------|------------|
| Installation | [Installation Guide](installation.md) |
| Export package restore | [Data Seeding and Migration](data-seeding.md) |
| Apache / uWSGI / Celery / SSL | [Deployment](deployment.md) |
| GeoServer | [GeoServer Management](administration/geoserver.md) |
| Logs | [Logs Management](administration/logs-management.md) |
| Architecture | [Technology and Workflow](technology-and-workflow.md) |

---

## Phase 0 — Before you start

- [ ] Target server ready (OS, admin access, sizing agreed)
- [ ] Export package received: `etoolkit_export_YYYYMMDD` (or `.tar.gz`)
- [ ] Application source code / repository access
- [ ] GeoServer plan (dedicated instance or shared; workspace **`etoolkit` only**)
- [ ] Secrets available for target (to be set in `.env` / vault — **not** inside the export package):
  - [ ] Postgres password
  - [ ] GeoServer admin password
  - [ ] `OPENAI_API_KEY` 
  - [ ] `EE_KEY` (Earth Engine service account JSON)
  - [ ] SMTP / email credentials (or IsDB mail relay)
- [ ] Domain / DNS name known (for Apache + SSL)
- [ ] Outbound network allowlist agreed (GEE/GCS, OpenAI, SMTP, basemap/WMS CDNs as needed)

---

## Phase 1 — System and dependencies

See [Installation Guide](installation.md).

- [ ] OS packages installed (PostgreSQL, PostGIS, Redis, GDAL, Apache, build tools, Python 3)
- [ ] Prefer **tested versions** where possible (see Installation → Specific versions)
- [ ] Redis running
- [ ] PostgreSQL running
- [ ] GDAL available: `gdalinfo --version`
- [ ] Application user/dirs planned (e.g. project under `/home/<user>/etoolkit`)

---

## Phase 2 — Application code and Python env

- [ ] Clone / deploy EO-Toolkit code to the new server
- [ ] Create and activate virtualenv
- [ ] Install dependencies (`requirements-server.txt` or project requirements)
- [ ] Create `media/` and `log/` directories if missing
- [ ] Copy `.env.example` → `.env` (values filled in Phase 4)

---

## Phase 3 — Receive and verify export package

See [Migration using the export package](data-seeding.md#migration-using-the-export-package).

### Expected package layout

```text
etoolkit_export_YYYYMMDD/
├── database/
│   ├── etoolkit_YYYYMMDD.dump      # preferred
│   ├── etoolkit_YYYYMMDD.sql       # optional
│   └── etoolkit_YYYYMMDD.sql.gz    # optional
├── media/
│   └── media_YYYYMMDD.tar.gz       # full MEDIA_ROOT
└── manifest/
    ├── SHA256SUMS
    ├── sizes.txt
    └── README.txt
```

- [ ] Package unpacked on the new server
- [ ] Contents match layout above (`database/`, `media/`, `manifest/`)
- [ ] Checksums reviewed (`sha256sum` vs `manifest/SHA256SUMS`)
- [ ] Sizes sensible (example package ~1 GB total; media dominates)

---

## Phase 4 — Database restore

- [ ] Create DB user and database
- [ ] `CREATE EXTENSION postgis;`
- [ ] Restore dump:
  - [ ] Prefer: `pg_restore ... --no-owner --no-acl database/*.dump`
  - [ ] Or: `psql ... -f database/*.sql`
- [ ] Confirm PostGIS: `SELECT PostGIS_Version();`
- [ ] Spot-check tables / user model exists (`\dt *user*`)

---

## Phase 5 — Restore `media/`

- [ ] Unpack `media/media_*.tar.gz` into Django project so you get:

```text
etoolkit/media/
├── geoserver_data/
├── reports/
└── custom_app/
```

- [ ] Permissions OK for app user (`chmod` / ownership)
- [ ] Spot-check: `ls media/geoserver_data`, `ls media/reports`

---

## Phase 6 — Configuration (`.env`)

- [ ] `DJANGO_ENV=production` (or equivalent)
- [ ] Postgres: `POSTGRES_DB`, `POSTGRES_USER`, `POSTGRES_PASSWORD`, `POSTGRES_HOST`, `POSTGRES_PORT`
- [ ] GeoServer: `GEOSERVER_URL`, `GEOSERVER_WMS_URL`, `GEOSERVER_USER`, `GEOSERVER_PASSWORD`, `GEOSERVER_WORKSPACE=etoolkit`
- [ ] `OPENAI_API_KEY`, `EE_KEY` (if features required)
- [ ] Email / `ADMIN_EMAIL` (or IsDB SMTP)
- [ ] `BASE_URL` / CSRF trusted hosts aligned with public URL (if used)
- [ ] Secrets stored securely (file permissions; prefer vault injection later)

---

## Phase 7 — GeoServer (eToolkit workspace only)

See [GeoServer Management](administration/geoserver.md).

- [ ] GeoServer installed and running (internal; not public if behind Apache)
- [ ] Workspace **`etoolkit`** created (do not import other tools’ workspaces)
- [ ] `.env` points at this GeoServer
- [ ] Publish layers from restored `media/geoserver_data/`:
  - [ ] `python manage.py seed --only geoserver_data`  
    **or** Admin → Geo Data Uploads / bulk upload
- [ ] WMS test for at least one layer

---

## Phase 8 — Django finalize

- [ ] `python manage.py migrate`
- [ ] `python manage.py collectstatic --noinput`
- [ ] Superuser available (from dump or `createsuperuser`)
- [ ] `python manage.py check` passes

---

## Phase 9 — Production services (deploy stack)

See [Deployment](deployment.md).

- [ ] uWSGI configured and systemd service enabled
- [ ] Celery worker(s) configured and systemd service enabled (Redis broker)
- [ ] Apache vhost: static/media aliases, reverse proxy to app, HTTP→HTTPS
- [ ] SSL certificates installed (Certbot or corporate certs)
- [ ] Services enabled on boot: `etoolkit_uwsgi`, `etoolkit_celery`, `apache2`, Redis, PostgreSQL
- [ ] Firewall / NSG: **inbound** 80/443 only (public); Postgres/Redis/GeoServer internal

---

## Phase 10 — Post-migration smoke tests

Do not hand over until these pass.

### Data / app

- [ ] `python manage.py showmigrations` / `check`
- [ ] Admin login works
- [ ] Home page loads (HTTPS)
- [ ] Catalog pages load (case studies, training, partners, data catalog as applicable)
- [ ] Map / WMS layer loads from workspace `etoolkit`
- [ ] Existing report/media file opens (from restored `media/reports/`)
- [ ] New analysis/report job runs (Celery) — if GEE/OpenAI configured

### Infrastructure

- [ ] `systemctl status` healthy for uWSGI, Celery, Apache
- [ ] Logs writable; no critical errors on startup ([Logs Management](administration/logs-management.md))
- [ ] Outbound calls succeed (GEE / OpenAI / SMTP as required)

---

## Phase 11 — Handover / production readiness

- [ ] DNS points to new server
- [ ] SSL valid for public hostname
- [ ] Backup plan agreed (DB + `media/` at minimum)
- [ ] Log retention / rotation noted
- [ ] Monitoring/alerting owner identified (if required by host org)
- [ ] Credentials transferred via secure channel only
- [ ] Sign-off: migration smoke tests completed

---

## Quick path summary

```text
1. Install OS deps + Redis + Postgres/PostGIS + GDAL + Apache
2. Deploy code + venv + requirements
3. Verify export package (database + media + manifest)
4. Restore DB dump → unpack media/ → configure .env
5. GeoServer workspace etoolkit → publish layers from media/geoserver_data
6. migrate + collectstatic
7. uWSGI + Celery + Apache + SSL
8. Smoke tests → go-live
```

**Fresh install instead of migration?** Skip the export package; use empty DB + [seeding](data-seeding.md#seeding-catalog-data) instead of dump/media restore, then continue from Phase 6–11.

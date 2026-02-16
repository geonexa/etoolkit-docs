# EO-Toolkit Documentation
This directory contains the complete documentation for EO-Toolkit, built with MkDocs.

## Quick Start

### Install Dependencies

```bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

### Serve Documentation Locally

```bash
mkdocs serve
```

The documentation will be available at `http://localhost:8000`


### Build Documentation

```bash
mkdocs build
```

This creates a `site/` directory with static HTML files.

### Deploy Documentation

```bash
mkdocs gh-deploy
```

This deploys to GitHub Pages (if configured).

## Documentation Structure

- `docs/` - Source markdown files
- `mkdocs.yml` - MkDocs configuration
- `requirements.txt` - Python dependencies

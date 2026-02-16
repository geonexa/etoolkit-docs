# API Endpoints

Complete reference for all API endpoints in EO-Toolkit.

## Catalog Data

### Get Catalog Data

Retrieve catalog items with filtering and pagination.

**Endpoint**: `GET /api/catalog-data/`

**Query Parameters**:
- `q` (string): Search query
- `data` (string): Data type (`thematic_data`, `training`, `case_studies`, `custom_apps`)
- `page` (integer): Page number (default: 1)

**Filter Parameters** (varies by data type):
- `gee_categories[]`: GEE categories
- `theme[]`: Themes
- `project_phase[]`: Project phases
- `partner[]`: Partners
- `category[]`: Categories
- `source[]`: Sources
- `language[]`: Languages
- `publisher[]`: Publishers
- `type[]`: Types
- `issued_year[]`: Years
- `keywords[]`: Keywords

**Response**:
```json
{
  "results": [...],
  "page": 1,
  "total_pages": 10,
  "total_items": 300,
  "filters": {...}
}
```

**Authentication**: Required

## Area Management

### Get Added Areas List

Get list of areas for a country.

**Endpoint**: `POST /api/getAddedAreasList/`

**Request Body**:
```json
{
  "countryName": "Niger"
}
```

**Response**:
```json
[
  {
    "id": 1,
    "name": "Area Name"
  }
]
```

**Authentication**: Required

### Get Area Geometry

Get geometry for a specific area.

**Endpoint**: `GET /api/getAreaGeometry/<area_id>/`

**Response**:
```json
{
  "id": 1,
  "name": "Area Name",
  "country": "Niger",
  "geometry": {...}
}
```

**Authentication**: Required

### Add Drawn Feature

Add an area drawn on the map.

**Endpoint**: `POST /api/addDrawFeature/`

**Request Body**:
```json
{
  "featureName": "My Area",
  "geom": "{\"type\":\"Polygon\",\"coordinates\":[...]}",
  "countryName": "Niger"
}
```

**Response**:
```json
{
  "result": "Feature saved",
  "featid": 1
}
```

**Authentication**: Required

### Add Uploaded Layer

Upload a GeoJSON file as an area.

**Endpoint**: `POST /api/addUploadedLayer/`

**Request Body** (form-data):
- `featureName`: Area name
- `countryName`: Country name
- `file`: GeoJSON file content (string)

**Response**:
```json
{
  "result": "Feature has been saved in your added areas."
}
```

**Authentication**: Required

### Delete Added Area

Delete an area.

**Endpoint**: `GET /api/deleteAddedArea/<area_id>`

**Response**:
```json
{
  "result": "Selected Area deleted"
}
```

**Authentication**: Required

## Data Visualization

### Get GEE Map Tile

Get tile URL for Google Earth Engine dataset.

**Endpoint**: `POST /api/get-data-tile/`

**Request Body**:
```json
{
  "selected_data_id": 1,
  "selected_geom": {...},
  "selectedYear": 2020
}
```

**Response**:
```json
{
  "tile_url": "https://...",
  "layer_name": "layer_name",
  "extent": [minLon, minLat, maxLon, maxLat],
  "legend": {...}
}
```

**Authentication**: Required

### Get GeoServer Layer

Get GeoServer layer information.

**Endpoint**: `POST /api/get-geoserver-layer/`

**Request Body**:
```json
{
  "selected_data_id": 1
}
```

**Response**:
```json
{
  "tile_url": "https://...",
  "layer_name": "layer_name",
  "extent": [minLon, minLat, maxLon, maxLat],
  "legend": {
    "type": "image",
    "url": "https://..."
  }
}
```

**Authentication**: Required

## Analysis

### Run Analysis

Execute an analysis.

**Endpoint**: `POST /api/runAnalysis/`

**Request Body**:
```json
{
  "selected_geom": {...},
  "analysis_type": "LULC"
}
```

**Analysis Types**:
- `LULC`: Land Use Land Cover
- `PCP`: Precipitation
- `ETa`: Actual Evapotranspiration
- `ssp245`: Climate Change SSP245
- `ssp585`: Climate Change SSP585
- `GRACE`: Water Storage

**Response**:
```json
{
  "analysis_type": "LULC",
  "data": [...]
}
```

**Authentication**: Required

### Generate Analysis Report

Generate a comprehensive PDF report.

**Endpoint**: `POST /api/generateReport/`

**Request Body**:
```json
{
  "areaid": 1,
  "theme": "Agriculture",
  "phase": "Planning",
  "country": "Niger"
}
```

**Response**:
```json
{
  "result": "Running",
  "task_id": "task-id-here"
}
```

**Authentication**: Required

## Task Management

### Get Task Status

Check status of an asynchronous task.

**Endpoint**: `GET /api/get-task-status/<task_id>/`

**Response**:
```json
{
  "state": "PROGRESS",
  "progress": 50,
  "total": 100,
  "status": "Processing..."
}
```

**States**:
- `PENDING`: Task queued
- `PROGRESS`: Task running
- `SUCCESS`: Task completed
- `FAILURE`: Task failed

**Authentication**: Required

### Delete Task History

Delete a saved analysis/task.

**Endpoint**: `GET /api/deletetaskhistory/<task_id>/`

**Response**:
```json
{
  "result": "Saved Analysis deleted"
}
```

**Authentication**: Required

## Recommendations

### Get Recommendations Data

Get data recommendations based on theme, phase, and country.

**Endpoint**: `POST /api/recommendations-data-details/`

**Request Body**:
```json
{
  "theme": "Agriculture",
  "phase": "Planning",
  "country": "NER"
}
```

**Response**:
```json
{
  "DataRecommendations": [...],
  "TrainingMaterial": [...],
  "CaseStudies": [...],
  "SOP": [...],
  "selected_theme": "Agriculture",
  "selected_phase": "Planning",
  "selected_country": "Niger",
  "country_boundary": {...}
}
```

**Authentication**: Required

## Error Handling

### Common Errors

**400 Bad Request**:
- Missing required parameters
- Invalid parameter values
- Validation errors

**401 Unauthorized**:
- Not authenticated
- Invalid credentials
- Session expired

**404 Not Found**:
- Resource doesn't exist
- Invalid ID

**500 Internal Server Error**:
- Server error
- Processing failure

### Error Response Format

```json
{
  "error": "Error message",
  "status": 400
}
```

---

See also: [Authentication](authentication.md)

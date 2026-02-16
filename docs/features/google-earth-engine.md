# Google Earth Engine Integration

EO-Toolkit leverages Google Earth Engine (GEE) for powerful geospatial analysis and data access.

## Overview

Google Earth Engine integration provides:

- Access to vast EO data catalog
- Cloud-based processing
- Time series analysis
- Advanced analytics
- High-performance computing

## Authentication

### Service Account Setup

1. Create Google Cloud Project
2. Enable Earth Engine API
3. Create service account
4. Download service account key JSON
5. Configure in environment:

```env
EE_KEY='{"type":"service_account",...}'
```

### Initialization

GEE is initialized automatically when needed:

```python
import ee
# Service account credentials loaded from environment
ee.Initialize(credentials)
```

## Available Datasets

### Land Use Land Cover

- **ESA WorldCover**: Global land cover
- **MODIS Land Cover**: MODIS-based classification
- **Dynamic World**: Near real-time land cover

### Precipitation

- **CHIRPS**: Climate Hazards Group InfraRed Precipitation
- **GPM**: Global Precipitation Measurement
- **PERSIANN**: Precipitation Estimation from Remotely Sensed Information

### Evapotranspiration

- **WaPOR**: Water Productivity Open-access Portal
- **MODIS ET**: MODIS Evapotranspiration
- **SSEBop**: Operational Simplified Surface Energy Balance

### Climate Data

- **ERA5**: ECMWF Reanalysis
- **NEX-GDDP**: NASA Earth Exchange Global Daily Downscaled Projections
- **CMIP6**: Climate Model Intercomparison Project

### Water Storage

- **GRACE**: Gravity Recovery and Climate Experiment
- **GRACE-FO**: GRACE Follow-On

## Analysis Functions

### Tile Layer Generation

Generate map tile URLs for visualization:

```python
tile_url, layer_name, extent, legend = get_gee_map_tileLayer(
    dataset_id, geometry
)
```

### Time Series Analysis

Extract time series data:

```python
timeseries = get_gee_time_series_from_ImageCollection(
    selected_geom=geometry,
    ImageCollection=collection,
    band="precipitation",
    scale=5000
)
```

### Image Collection Listing

List available images in collection:

```python
images = list_collection_images(
    collection_id, start_date, end_date
)
```

## Dataset-Specific Functions

### CHIRPS Precipitation

```python
# Annual collection
collection = get_chirps_annual_precip_collection(
    start_year, end_year, aggregation, geometry
)

# Time series
timeseries = get_gee_time_series_from_ImageCollection(
    selected_geom=geometry,
    ImageCollection=collection,
    band="PCP",
    scale=5000
)
```

### WaPOR ETa

```python
# Collection
collection = get_wapor_eta_collection(
    start_year, end_year, aggregation, geometry
)

# Time series
timeseries = get_gee_time_series_from_ImageCollection(
    selected_geom=geometry,
    ImageCollection=collection,
    band="ETa",
    scale=300
)
```

### LULC Analysis

```python
# Pie chart data
lulc_data = get_lulc_pieChart(selected_geom=geometry)
```

### Climate Change Analysis

```python
# SSP245 scenario
result = climate_change_analysis(geometry, scenario='ssp245')

# SSP585 scenario
result = climate_change_analysis(geometry, scenario='ssp585')
```

### Water Storage Analysis

```python
# GRACE analysis
timeseries = water_storage_analysis(selected_geom=geometry)
```

## Data Processing

### Geometry Handling

- Accepts GeoJSON geometries
- Converts to EE Geometry objects
- Handles polygons and multipolygons
- Validates geometry

### Scale and Resolution

- Appropriate scale for each dataset
- Balances accuracy and performance
- Configurable per analysis

### Temporal Filtering

- Date range selection
- Image collection filtering
- Time series aggregation

## Performance Considerations

### Processing Time

- Depends on area size
- Dataset complexity
- Temporal range
- GEE server load

### Optimization Tips

- Use appropriate scales
- Limit temporal ranges
- Reduce area size when possible
- Cache results when applicable

## Error Handling

### Common Issues

- **Quota Exceeded**: GEE quota limits
- **Invalid Geometry**: Geometry validation errors
- **Dataset Not Found**: Invalid dataset ID
- **Timeout**: Long-running operations

### Solutions

- Check GEE quotas
- Validate geometries
- Verify dataset IDs
- Use smaller areas/dates
- Retry failed operations

## Best Practices

1. **Validate Geometries**: Ensure valid GeoJSON
2. **Appropriate Scales**: Use dataset-appropriate scales
3. **Limit Temporal Range**: Don't request excessive date ranges
4. **Error Handling**: Implement proper error handling
5. **Caching**: Cache results when possible
6. **Monitoring**: Monitor GEE quota usage

## Dataset Catalog

### JSON Configuration

Datasets are configured in `gee_datasets.json`:

```json
{
  "DataID": 1,
  "title": "Dataset Name",
  "description": "Description",
  "gee_categories": ["category1"],
  "theme": "Agriculture",
  "project_phase": "Planning",
  "partner": "Provider",
  "spatial_extent": [minLon, minLat, maxLon, maxLat],
  "temporal_coverage": "2000-2024",
  "gee_collection": "collection/path"
}
```

### Adding New Datasets

1. Add entry to `gee_datasets.json`
2. Configure collection path
3. Set appropriate metadata
4. Test dataset access
5. Update documentation

## Security

### Credentials Management

- Store service account key securely
- Use environment variables
- Never commit keys to repository
- Rotate keys regularly

### Access Control

- GEE quotas apply
- Monitor usage
- Implement rate limiting
- Handle errors gracefully

---

Next: [Analysis Types](analysis-types.md)

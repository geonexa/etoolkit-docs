# GeoServer Integration

EO-Toolkit integrates with GeoServer to manage and serve spatial data layers.

## Overview

GeoServer integration enables:

- Upload and publish spatial data
- Serve data via WMS/WFS
- Style layers with SLD files
- Manage raster and vector data
- Integrate with map viewers

## Supported Data Types

### Raster Data

- **Format**: GeoTIFF (.tif, .tiff)
- **Projection**: EPSG:4326 (WGS84) or auto-detected
- **Bands**: Single or multi-band
- **Compression**: Various compression formats supported

### Vector Data

- **Format**: Shapefile (ZIP archive)
- **Geometry Types**: Polygon, MultiPolygon
- **Attributes**: Preserved in GeoServer
- **Projection**: EPSG:4326 (WGS84)

## Uploading Data

### Through Admin Panel

1. Log in as administrator
2. Navigate to Django Admin
3. Go to **Geo Data Uploads**
4. Click **Add Geo Data Upload**
5. Fill in form:
   - Name (unique)
   - Description
   - Data Provider
   - Data File (GeoTIFF or Shapefile ZIP)
   - Thumbnail Image
   - Store Type (GeoTIFF or Shapefile)
   - Keywords (comma-separated)
   - SLD File (optional)
6. Click **Save**

### Automatic Processing

Upon upload:

1. File is validated
2. Data is copied to media directory
3. GeoServer store is created
4. Layer is published
5. WMS service is configured
6. Metadata is stored in database

## Layer Configuration

### Workspace

- Default workspace: `etoolkit`
- Configurable in settings
- All layers published to workspace

### Layer Naming

- Uses dataset name
- Sanitized for GeoServer
- Must be unique within workspace

### Styling

- **SLD Files**: Upload custom styles
- **Default Styles**: Auto-applied if no SLD
- **Legend**: Generated automatically

## Accessing Layers

### WMS Service

Layers are accessible via WMS:

```
{GEOSERVER_WMS_URL}/{workspace}/wms?service=WMS&version=1.1.1&request=GetMap&layers={workspace}:{layer_name}&styles=&bbox={bbox}&width=256&height=256&srs=EPSG:3857&format=image/png
```

### In Application

- Layers appear in Data Catalog
- Can be viewed on map
- Extent information available
- Legend URLs provided

## Bulk Upload

### Preparation

1. Organize data files:
   ```
   /media/geoserver_data/{folder_name}/
   ├── {dataset_name}.tif
   ├── {dataset_name}.sld
   └── {dataset_name}.png
   ```

2. Create metadata JSON:
   ```json
   [
     {
       "name": "Dataset Name",
       "description": "Description",
       "data_provider": "Provider",
       "store_type": "geotiff",
       "keywords": "keyword1, keyword2"
     }
   ]
   ```

3. Place JSON at: `/staticData/geoserver_data.json`

### Triggering Bulk Upload

1. Go to Django Admin
2. Navigate to **Geo Data Uploads**
3. Click **Trigger Bulk Upload** button
4. System processes all entries
5. Check logs for status

## Layer Management

### Viewing Layers

- List all layers in admin
- View layer details
- Check GeoServer status
- Verify WMS availability

### Updating Layers

- Edit metadata in admin
- Replace data file (re-upload)
- Update styles (upload new SLD)
- Modify keywords/description

### Deleting Layers

- Delete from admin panel
- Automatic cleanup:
  - GeoServer layer removed
  - Store deleted
  - Files removed from disk

## Troubleshooting

### Upload Fails

**Solutions**:
- Check file format
- Verify file permissions
- Check GeoServer connection
- Review server logs
- Verify workspace exists

### Layer Not Appearing

**Solutions**:
- Check GeoServer logs
- Verify layer published
- Test WMS URL directly
- Check workspace name
- Verify permissions

### Style Issues

**Solutions**:
- Validate SLD syntax
- Check style compatibility
- Verify style name matches
- Test in GeoServer admin

### Performance Issues

**Solutions**:
- Optimize raster files
- Use appropriate resolution
- Consider tiling
- Check GeoServer configuration

## Best Practices

1. **File Organization**: Keep files organized
2. **Naming**: Use descriptive, unique names
3. **Metadata**: Provide complete metadata
4. **Styles**: Create appropriate SLD files
5. **Thumbnails**: Include preview images
6. **Keywords**: Use relevant keywords
7. **Testing**: Test layers after upload

## Configuration

### Settings

Configure in `settings.py`:

```python
GEOSERVER_URL = 'http://localhost:8080/geoserver'
GEOSERVER_WMS_URL = 'http://localhost:8080/geoserver'
GEOSERVER_USER = 'admin'
GEOSERVER_PASSWORD = 'geoserver'
GEOSERVER_WORKSPACE = 'etoolkit'
```

### GeoServer Setup

1. Install GeoServer
2. Create workspace
3. Configure credentials
4. Set up data directory
5. Test connection

---

Next: [Google Earth Engine Integration](google-earth-engine.md)

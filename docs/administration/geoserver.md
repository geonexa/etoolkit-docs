# GeoServer Management

Guide for managing GeoServer integration in EO-Toolkit.

## GeoServer Setup

### Installation

1. Download GeoServer from [geoserver.org](https://geoserver.org/download/)
2. Install and configure
3. Start GeoServer service
4. Access admin at `http://localhost:8080/geoserver`

### Workspace Configuration

1. Log into GeoServer admin
2. Navigate to **Workspaces**
3. Create workspace: `etoolkit`
4. Set as default if needed

### User Configuration

Set GeoServer credentials in your `.env` file (never commit real credentials):

```python
GEOSERVER_USER = 'admin'
GEOSERVER_PASSWORD = 'your_password'
```

## Data Upload Process

### Single Upload

1. Use Django Admin panel
2. Go to **Geo Data Uploads**
3. Add new upload
4. System automatically:
   - Creates store
   - Publishes layer
   - Configures WMS

### Bulk Upload

1. Organize files:
   ```
   /media/geoserver_data/{dataset_name}/
   ├── {dataset_name}.tif
   ├── {dataset_name}.sld
   └── {dataset_name}.png
   ```

2. Create metadata JSON
3. Place at `/staticData/geoserver_data.json`
4. Trigger bulk upload from admin

## Layer Management

### Viewing Layers

- List in Django Admin
- View in GeoServer admin
- Check WMS availability
- Verify layer properties

### Updating Layers

- Edit metadata in Django Admin
- Replace data file (re-upload)
- Update styles (upload new SLD)
- Modify layer properties in GeoServer

### Deleting Layers

- Delete from Django Admin
- Automatic cleanup:
  - GeoServer layer removed
  - Store deleted
  - Files removed

## Style Management

### SLD Files

- Upload with data
- Applied automatically
- Can be updated
- Supports various styles

### Default Styles

- Applied if no SLD provided
- GeoServer default styles
- Can be customized

## Troubleshooting

### Connection Issues

- Verify GeoServer is running
- Check URL and credentials
- Test connection manually
- Review network settings

### Upload Failures

- Check file format
- Verify permissions
- Check workspace exists
- Review GeoServer logs

### Layer Not Appearing

- Check layer published
- Verify WMS enabled
- Test WMS URL directly
- Check layer visibility

### Performance Issues

- Optimize raster files
- Use appropriate resolution
- Consider tiling
- Check GeoServer config

## Best Practices

1. **Organize Files**: Keep files organized
2. **Naming**: Use descriptive names
3. **Metadata**: Provide complete metadata
4. **Styles**: Create appropriate SLD files
5. **Testing**: Test layers after upload
6. **Backup**: Backup GeoServer configuration

---

Next: [Bulk Data Upload](bulk-upload.md)

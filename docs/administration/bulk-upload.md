# Bulk Data Upload

Guide for bulk uploading spatial data to GeoServer.

## Preparation

### File Organization

Organize data files in the following structure:

```
/media/geoserver_data/{folder_name}/
├── {dataset_name}.tif          # GeoTIFF file (for raster)
├── {dataset_name}.zip          # Shapefile ZIP (for vector)
├── {dataset_name}.sld          # Style file (optional)
└── {dataset_name}.png          # Thumbnail image
```

### Example Structure

```
/media/geoserver_data/Niger_LULC/
├── Niger_LULC.tif
├── Niger_LULC.sld
└── Niger_LULC.png
```

## Metadata JSON

### Location

Place metadata JSON at:
```
/staticData/geoserver_data.json
```

### JSON Format

```json
[
  {
    "name": "Dataset Name",
    "description": "Dataset description",
    "data_provider": "Provider Name",
    "store_type": "geotiff",
    "keywords": "keyword1, keyword2, keyword3",
    "data_file": "geoserver_data/folder_name/dataset.tif",
    "thumbnail": "geoserver_data/folder_name/thumbnail.png",
    "sld_file": "geoserver_data/folder_name/style.sld"
  }
]
```

### Field Descriptions

- **name**: Unique dataset name (required)
- **description**: Dataset description (optional)
- **data_provider**: Data provider name (required)
- **store_type**: `"geotiff"` or `"shapefile"` (required)
- **keywords**: Comma-separated keywords (required)
- **data_file**: Path to data file (required)
- **thumbnail**: Path to thumbnail (required)
- **sld_file**: Path to SLD file (optional)

## Upload Process

### Step 1: Prepare Files

1. Organize data files in folders
2. Create thumbnails for each dataset
3. Prepare SLD files (optional)
4. Verify file paths

### Step 2: Create Metadata JSON

1. Create JSON file with all datasets
2. Verify JSON syntax
3. Check file paths are correct
4. Place at `/staticData/geoserver_data.json`

### Step 3: Trigger Upload

1. Log into Django Admin
2. Navigate to **Geo Data Uploads**
3. Click **Trigger Bulk Upload** button
4. System processes all entries

### Step 4: Monitor Progress

1. Check Django logs
2. Verify uploads in admin
3. Test layers in GeoServer
4. Check WMS availability

## Processing Details

### What Happens

For each entry in JSON:

1. **Validation**: Check if dataset exists
2. **File Check**: Verify file paths exist
3. **Database**: Create GeoDataUpload record
4. **GeoServer**: Upload to GeoServer
5. **Publish**: Publish as WMS layer
6. **Style**: Apply SLD if provided

### Error Handling

- Skips existing datasets
- Logs errors for failed uploads
- Continues with remaining entries
- Reports final status

## Best Practices

1. **Test First**: Test with one dataset first
2. **Validate JSON**: Check JSON syntax
3. **Verify Paths**: Ensure all paths correct
4. **Check Files**: Verify all files exist
5. **Unique Names**: Ensure unique dataset names
6. **Backup**: Backup before bulk upload
7. **Monitor**: Watch logs during upload

## Troubleshooting

### Upload Fails

- Check JSON syntax
- Verify file paths
- Check file permissions
- Review error messages
- Check GeoServer connection

### Some Datasets Fail

- Check individual file issues
- Verify file formats
- Check GeoServer logs
- Review error messages

### Duplicate Names

- Ensure unique names in JSON
- Check existing datasets
- Remove duplicates from JSON

### File Not Found

- Verify file paths
- Check file locations
- Ensure files uploaded
- Verify permissions

## Example

### Complete Example

**File Structure**:
```
/media/geoserver_data/
├── Dataset1/
│   ├── Dataset1.tif
│   ├── Dataset1.sld
│   └── Dataset1.png
└── Dataset2/
    ├── Dataset2.zip
    ├── Dataset2.sld
    └── Dataset2.png
```

**JSON** (`/staticData/geoserver_data.json`):
```json
[
  {
    "name": "Dataset1",
    "description": "First dataset",
    "data_provider": "Provider A",
    "store_type": "geotiff",
    "keywords": "agriculture, landcover",
    "data_file": "geoserver_data/Dataset1/Dataset1.tif",
    "thumbnail": "geoserver_data/Dataset1/Dataset1.png",
    "sld_file": "geoserver_data/Dataset1/Dataset1.sld"
  },
  {
    "name": "Dataset2",
    "description": "Second dataset",
    "data_provider": "Provider B",
    "store_type": "shapefile",
    "keywords": "boundaries, administrative",
    "data_file": "geoserver_data/Dataset2/Dataset2.zip",
    "thumbnail": "geoserver_data/Dataset2/Dataset2.png",
    "sld_file": "geoserver_data/Dataset2/Dataset2.sld"
  }
]
```

---

Return to: [Admin Panel](admin-panel.md)

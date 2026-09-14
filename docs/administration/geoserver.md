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

## Helper script: TIFF to COG

For efficient web visualization and GeoServer uploads, GeoTIFFs can be converted to **Cloud Optimized GeoTIFF (COG)** format. Use the script below to compress GeoTIFFs into COG format (tiled, DEFLATE-compressed) using GDAL.

### Prerequisites

- **GDAL** installed and available on your `PATH` (e.g. `gdal_translate`).
- On Ubuntu: `sudo apt-get install gdal-bin`

### How to use

1. **Prepare your folder structure** (same as for bulk upload):

   ```
   geoserver_data/
   ├── DatasetName1/
   │   └── DatasetName1.tif
   ├── DatasetName2/
   │   └── DatasetName2.tif
   ...
   ```

   Each subfolder name must match the `.tif` filename (e.g. folder `Niger_LULC` with `Niger_LULC.tif`).

2. **Copy the script** from the collapsible block below. Save as `gdal_tiff_to_cog.py`. Set `base_dir` inside the script to your data path (e.g. `"/path/to/etoolkit/media/geoserver_data"` or keep `"geoserver_data"` if running from the directory that contains it).

3. **Run the script:**

   ```bash
   python gdal_tiff_to_cog.py
   ```

4. The script will:
   - Find each `{folder_name}.tif` under `base_dir`
   - Compress it to COG (tiled, DEFLATE) as `{folder_name}_compressed.tif`
   - Replace the original file with the compressed one

??? "View script: gdal_tiff_to_cog.py"
    Copy the script below, save as `gdal_tiff_to_cog.py`, set `base_dir` to your `geoserver_data` path, and run with `python gdal_tiff_to_cog.py`.

    ```python
    # This script compresses all GeoTIFF files within subfolders
    # into Cloud Optimized GeoTIFF (COG) format using GDAL. The optimized files are suitable
    # for efficient web visualization and upload to GeoServer.

    import os
    import subprocess

    # Path to the main directory containing subfolders
    base_dir = "geoserver_data"

    # Loop through each subdirectory
    for folder in os.listdir(base_dir):
        folder_path = os.path.join(base_dir, folder)
        if os.path.isdir(folder_path):
            tif_filename = f"{folder}.tif"
            tif_path = os.path.join(folder_path, tif_filename)

            if os.path.isfile(tif_path):
                compressed_path = os.path.join(folder_path, f"{folder}_compressed.tif")

                print(f"Compressing: {tif_path}")
                cmd = [
                    "gdal_translate",
                    "-co", "COMPRESS=DEFLATE",
                    "-co", "TILED=YES",
                    "-of", "GTiff",
                    tif_path,
                    compressed_path
                ]

                try:
                    subprocess.check_call(cmd)
                    os.replace(compressed_path, tif_path)
                    print(f"Replaced with compressed: {tif_path}")
                except subprocess.CalledProcessError as e:
                    print(f"Compression failed for {tif_path}: {e}")
            else:
                print(f"No .tif found: {tif_path}")
    ```

## Bulk geoserver data upload
```
  1. first All data files must be organized under:
  /media/geoserver_data/<folder_name>/
  Each dataset should have:
  - GeoTIFF or Shapefile ZIP
  - .sld file for styling
  - Thumbnail image 

  media/geoserver_data/Niger_LULC/
  ├── Niger_LULC.tif
  ├── Niger_LULC.sld
  ├── Niger_LULC.png

  2. Place the bulk metadata JSON at:
  /staticData/geoserver_data.json

  3. What the Script Does
  - Loads and parses geoserver_data.json

  - For each entry:
    - Checks if a dataset with the same name already exists
    - Validates file paths
    - Saves a new GeoDataUpload model instance
    - Uploads the data and style to GeoServer

  4. Triggering Upload
  - Go to Geo Data Upload in Django Admin
  - Click the top-right button: “Trigger Bulk Upload”
```
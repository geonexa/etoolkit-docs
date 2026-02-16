# Area Management Guide

Areas of Interest (AOIs) are fundamental to running analyses in EO-Toolkit. This guide covers creating, managing, and using areas.

## What are Areas?

Areas define the geographic boundaries for your analyses. They can be:

- **Drawn directly** on the map
- **Uploaded** as GeoJSON files
- **Saved** for reuse across multiple analyses
- **Organized** by country

## Creating Areas

### Method 1: Drawing on Map

1. Navigate to the map interface (Data Map or Search page)
2. Click the **Draw Polygon** tool
3. Click on the map to create vertices:
   - Each click adds a vertex
   - Double-click to finish the polygon
   - Right-click to cancel
4. A form will appear:
   - **Area Name**: Enter a descriptive name
   - **Country**: Select the country
5. Click **Save Area**

!!! tip "Drawing Tips"
    - Start with a small area for testing
    - Use zoom to draw precisely
    - You can edit vertices before saving
    - Polygons must be closed shapes

### Method 2: Uploading GeoJSON

1. Prepare your GeoJSON file:
   - Must contain Polygon or MultiPolygon geometries
   - Should be in EPSG:4326 (WGS84) coordinate system
   - Can contain multiple features (will be merged)

2. Navigate to the map interface
3. Click **Upload Area** or **Import GeoJSON**
4. Select your GeoJSON file
5. Fill in the form:
   - **Area Name**: Descriptive name
   - **Country**: Select country
6. Click **Upload**

!!! warning "File Requirements"
    - Supported formats: GeoJSON (.geojson, .json)
    - Geometry types: Polygon or MultiPolygon only
    - Coordinate system: EPSG:4326 (will be converted if needed)
    - Maximum file size: Check system limits

### Area Validation

The system validates uploaded areas:

- **Geometry Type**: Only Polygon/MultiPolygon accepted
- **Coordinate System**: Converts to EPSG:4326 if needed
- **Z-coordinates**: Automatically removed if present
- **Duplicate Names**: Prevents duplicate names per country

## Managing Areas

### Viewing Your Areas

1. Access the area selector (usually in map interface)
2. Select a country from the dropdown
3. View list of your saved areas for that country
4. Click an area to load it on the map

### Area List Features

- **Name**: Area identifier
- **Country**: Associated country
- **Actions**: View, Edit, Delete options

### Editing Areas

Currently, areas cannot be edited directly. To modify:

1. Delete the existing area
2. Create a new area with updated geometry
3. Or contact support for assistance

### Deleting Areas

1. Select the area from the list
2. Click **Delete** or trash icon
3. Confirm deletion
4. Area and associated data are removed

!!! warning "Deletion Warning"
    Deleting an area will also delete:
    - Associated analysis results
    - Generated reports
    - Task history
    
    Consider exporting important data before deletion.

## Using Areas

### Selecting an Area for Analysis

1. Ensure you're on a page with analysis tools
2. Open the area selector
3. Choose a country
4. Select an area from the list
5. The area boundary appears on the map
6. Proceed with your analysis

### Area Requirements for Analysis

Different analyses have different requirements:

- **Spatial Coverage**: Area must be within data coverage
- **Size**: Very large areas may take longer to process
- **Geometry**: Must be valid polygon geometry

## Area Organization

### Best Practices

1. **Naming Convention**: Use descriptive names
   - Example: "Niger_Agriculture_Zone_1"
   - Include location and purpose

2. **Country Organization**: Group areas by country
   - Makes management easier
   - Enables country-specific analyses

3. **Version Control**: For updated areas:
   - Add version numbers: "Area_v1", "Area_v2"
   - Or use dates: "Area_2024_01"

4. **Documentation**: Note area purpose:
   - Use descriptive names
   - Consider keeping notes elsewhere

### Area Limits

- **Per User**: No hard limit (check system capacity)
- **Per Country**: Unlimited areas per country
- **Size**: Very large areas may impact performance

## GeoJSON Format

### Example GeoJSON Structure

```json
{
  "type": "FeatureCollection",
  "features": [
    {
      "type": "Feature",
      "geometry": {
        "type": "Polygon",
        "coordinates": [[
          [longitude1, latitude1],
          [longitude2, latitude2],
          [longitude3, latitude3],
          [longitude1, latitude1]
        ]]
      },
      "properties": {
        "name": "My Area"
      }
    }
  ]
}
```

### Creating GeoJSON Files

You can create GeoJSON files using:

- **QGIS**: Export as GeoJSON
- **ArcGIS**: Export to GeoJSON
- **Online Tools**: geojson.io, GeoJSON Editor
- **Python**: Using GeoPandas or similar libraries

### Coordinate System

- **Required**: EPSG:4326 (WGS84)
- **Automatic Conversion**: System converts if needed
- **Verification**: Check coordinates are valid lat/lon

## Troubleshooting

### Upload Failed

**Problem**: GeoJSON upload fails

**Solutions**:
- Verify file format (valid GeoJSON)
- Check geometry type (Polygon/MultiPolygon)
- Ensure coordinates are valid
- Check file size limits
- Verify coordinate system

### Area Not Appearing

**Problem**: Area doesn't show on map

**Solutions**:
- Check area is selected
- Verify map zoom level
- Refresh the page
- Check browser console for errors

### Cannot Delete Area

**Problem**: Delete operation fails

**Solutions**:
- Ensure you're the area owner
- Check for active analyses
- Try again after a moment
- Contact support if persistent

### Duplicate Name Error

**Problem**: "Area with this name already exists"

**Solutions**:
- Use a different name
- Add version number or date
- Delete old area if replacing
- Check country selection

### Invalid Geometry Error

**Problem**: "Unsupported geometry types"

**Solutions**:
- Convert to Polygon or MultiPolygon
- Use QGIS or similar tool to fix geometry
- Ensure geometry is valid
- Check for self-intersections

## Tips

1. **Start Small**: Test with small areas first
2. **Save Often**: Areas are saved automatically
3. **Backup**: Keep GeoJSON files as backups
4. **Organize**: Use consistent naming conventions
5. **Document**: Note area purposes and dates

---

Next: [Analysis Tools Guide](analysis-tools.md)

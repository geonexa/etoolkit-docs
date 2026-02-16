# Data Catalog Feature

The Data Catalog is a central feature providing access to Earth Observation datasets, training materials, case studies, and custom applications.

## Overview

The Data Catalog serves as a comprehensive repository for:

- Earth Observation datasets
- Educational resources
- Real-world examples
- External applications
- Partner information

## Catalog Types

### Thematic Data

Earth Observation datasets from multiple sources:

- **Google Earth Engine Datasets**: GEE catalog integration
- **GeoServer Layers**: Uploaded spatial data
- **Metadata**: Rich dataset information
- **Visualization**: Map previews and thumbnails

**Features**:
- Search and filter capabilities
- Detailed metadata
- Map visualization
- Download options (where available)

### Training Materials

Educational resources and tutorials:

- **Categories**: Organized by topic
- **Sources**: Various providers
- **Languages**: Multiple language support
- **Formats**: Documents, videos, guides

**Use Cases**:
- Learning EO concepts
- Tool tutorials
- Best practices
- Technical guides

### Case Studies

Real-world application examples:

- **Publishers**: Organizations and institutions
- **Types**: Various study types
- **Years**: Publication dates
- **Topics**: Subject areas

**Benefits**:
- Learn from examples
- Understand applications
- Get inspiration
- See best practices

### Custom Apps

External applications and tools:

- **Keywords**: Topic-based organization
- **Links**: Direct access
- **Descriptions**: App information
- **Thumbnails**: Visual previews

## Search and Discovery

### Search Functionality

- **Full-text Search**: Across titles and descriptions
- **Keyword Matching**: Intelligent keyword search
- **Instant Results**: Real-time filtering

### Filtering System

**Thematic Data Filters**:
- GEE Categories
- Theme
- Project Phase
- Partner/Provider

**Training Material Filters**:
- Category
- Source
- Language

**Case Study Filters**:
- Publisher
- Type
- Issued Year

**Custom App Filters**:
- Keywords

### Pagination

- 30 items per page
- Easy navigation
- Page indicators
- Total count display

## Dataset Details

### Information Displayed

- **Title**: Dataset name
- **Description**: Detailed information
- **Provider**: Data source
- **Keywords**: Relevant tags
- **Spatial Extent**: Coverage area
- **Temporal Coverage**: Time period
- **Thumbnail**: Visual preview

### Actions Available

- **View on Map**: Visualize dataset
- **View Details**: Full information
- **Download**: If available
- **Share**: Share dataset link

## GeoServer Integration

### Uploaded Datasets

Administrators can upload:

- **Raster Data**: GeoTIFF files
- **Vector Data**: Shapefiles
- **Styles**: SLD styling files
- **Thumbnails**: Preview images

### Layer Management

- Automatic GeoServer publishing
- WMS service generation
- Style application
- Metadata management

## Recommendations Engine

### Contextual Recommendations

Based on:
- Selected theme
- Project phase
- Country selection

### Recommendation Types

- **Datasets**: Relevant EO data
- **Training**: Educational materials
- **Case Studies**: Similar projects
- **SOPs**: Standard procedures

## Best Practices

1. **Use Filters**: Narrow down efficiently
2. **Read Metadata**: Understand data before use
3. **Check Coverage**: Verify spatial/temporal coverage
4. **Explore Related**: Discover connected resources
5. **Save Favorites**: Bookmark useful items

## Technical Details

### Data Sources

- Google Earth Engine API
- GeoServer WMS/WFS
- Static JSON catalogs
- Database queries

### Performance

- Optimized queries
- Caching where applicable
- Pagination for large results
- Efficient filtering

---

Next: [GeoServer Integration](geoserver.md)

# Technology Stack and How It Works

---

## Technology Stack

### Frontend Technologies

- **Django** – For fast, server-rendered UI delivery
- **HTML, CSS, JavaScript** – Core web technologies for UI and interactions
- **Material UI + jQuery** – Responsive design and UI interactions

### Geospatial and Mapping

- **OpenLayers** – Lightweight JavaScript library for interactive, customizable web maps
- **GeoServer** – Open-source map server for sharing raster and vector data via OGC standards
- **WMS (Web Map Service)** – For streaming geospatial raster data (e.g. LULC, NDVI)
- **Custom Tile Layers** – Integration of Google Earth Engine tiles for real-time Earth Observation data

### Backend Services

- **Django** – Python-based web framework for robust API and server logic
- **GDAL** – Geospatial Data Abstraction Library for raster and vector processing
- **Google Earth Engine API** – For accessing petabyte-scale EO data and on-the-fly analysis

### Database and Storage

- **PostgreSQL** – Relational database for structured project metadata
- **PostGIS** – Spatial extension for PostgreSQL, enabling spatial queries and geometry storage

---

## How It Works

The platform is designed to make the discovery, understanding, and application of Earth Observation (EO) resources simple and impactful. It follows a user-friendly workflow that enables project teams and stakeholders to move from exploration to informed action.

### 1. User Interface and Input

- Users begin by selecting filters, keywords, and parameters such as theme, project phase, and project site.
- Keyword search and spatial filtering allow targeted exploration.
- The interface dynamically captures user preferences and sends them as query parameters.

### 2. Backend Processing

- The server receives the query parameters and processes them against the pre-curated catalog of datasets, training materials, and case studies.
- Filtering logic is applied to return only relevant records based on user selections.
- Pagination is handled to efficiently serve large datasets, and available filter options are updated dynamically.

### 3. Results and Visualization

- Filtered results are returned and displayed as interactive cards with links, thumbnails, and descriptions.
- For selected datasets, users can explore spatial layers on an embedded map with legends and basemap controls.
- Data can also be further analyzed, visualized, or downloaded depending on its type and availability (e.g. raster layers, time series, EO maps).

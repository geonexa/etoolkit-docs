# Analysis Tools Guide

EO-Toolkit provides powerful analysis tools for Earth Observation data. This guide covers all available analysis types and how to use them.

## Overview

Available analysis types:

- **LULC**: Land Use Land Cover analysis
- **PCP**: Precipitation analysis (CHIRPS)
- **ETa**: Actual Evapotranspiration
- **Climate Change**: SSP245 and SSP585 scenarios
- **GRACE**: Water storage analysis

## Prerequisites

Before running analyses:

1. **Select an Area**: Choose or create an area of interest
2. **Check Data Availability**: Ensure data covers your area and time period
3. **Internet Connection**: Required for Google Earth Engine analyses

## Running an Analysis

### Basic Steps

1. **Select Area**: Choose your area of interest from the area selector
2. **Choose Analysis Type**: Select from available analysis types
3. **Configure Parameters**: Set any required parameters (dates, etc.)
4. **Run Analysis**: Click the run button
5. **View Results**: Results appear in charts and visualizations

### Analysis Interface

The analysis interface typically includes:

- **Area Selector**: Choose your area
- **Analysis Type Selector**: Select analysis type
- **Parameter Controls**: Date ranges, options, etc.
- **Run Button**: Execute the analysis
- **Results Panel**: View charts and data

## Analysis Types

### Land Use Land Cover (LULC)

**Purpose**: Analyze land cover types and their distribution

**Data Source**: Google Earth Engine (ESA WorldCover or similar)

**Output**:
- Pie chart showing land cover distribution
- Area statistics (hectares) per land cover type
- Percentage breakdown

**Land Cover Classes** (example):
- Cropland
- Grassland
- Tree Cover
- Shrubland
- Built-up
- Water Bodies
- Bare/Sparse Vegetation
- Wetlands

**Usage**:
1. Select your area
2. Choose "LULC" analysis type
3. Click "Run Analysis"
4. View pie chart and statistics

**Use Cases**:
- Agricultural planning
- Forest monitoring
- Urban development
- Environmental assessment

### Precipitation Analysis (PCP)

**Purpose**: Analyze precipitation patterns and trends

**Data Source**: CHIRPS (Climate Hazards Group InfraRed Precipitation with Station data)

**Parameters**:
- Start Year: Beginning of analysis period
- End Year: End of analysis period
- Aggregation: Annual, Monthly, etc.

**Output**:
- Time series chart of precipitation
- Annual/seasonal statistics
- Trend analysis

**Usage**:
1. Select your area
2. Choose "PCP" analysis type
3. Set date range (if applicable)
4. Click "Run Analysis"
5. View time series chart

**Use Cases**:
- Drought monitoring
- Water resource planning
- Agricultural planning
- Climate studies

### Actual Evapotranspiration (ETa)

**Purpose**: Analyze actual evapotranspiration rates

**Data Source**: WaPOR (Water Productivity Open-access Portal)

**Parameters**:
- Start Year: Beginning of analysis period
- End Year: End of analysis period
- Aggregation: Annual, Monthly, etc.

**Output**:
- Time series chart of ETa
- Seasonal patterns
- Trend analysis

**Usage**:
1. Select your area
2. Choose "ETa" analysis type
3. Set date range
4. Click "Run Analysis"
5. View time series chart

**Use Cases**:
- Irrigation planning
- Water management
- Agricultural productivity
- Drought assessment

### Climate Change Analysis

**Purpose**: Project future climate scenarios

**Scenarios Available**:
- **SSP245**: Moderate emissions scenario
- **SSP585**: High emissions scenario

**Parameters**:
- Scenario: SSP245 or SSP585
- Projection period: Future years

**Output**:
- Temperature projections
- Precipitation projections
- Trend analysis
- Comparison charts

**Usage**:
1. Select your area
2. Choose "Climate Change" analysis
3. Select scenario (SSP245 or SSP585)
4. Click "Run Analysis"
5. View projection charts

**Use Cases**:
- Climate adaptation planning
- Risk assessment
- Long-term planning
- Policy development

### Water Storage Analysis (GRACE)

**Purpose**: Analyze terrestrial water storage changes

**Data Source**: GRACE (Gravity Recovery and Climate Experiment)

**Output**:
- Time series of water storage
- Trends and anomalies
- Seasonal patterns

**Usage**:
1. Select your area
2. Choose "GRACE" analysis type
3. Click "Run Analysis"
4. View time series chart

**Use Cases**:
- Groundwater monitoring
- Water resource assessment
- Drought analysis
- Hydrological studies

## Understanding Results

### Charts and Visualizations

Results are displayed as:

- **Time Series Charts**: Temporal trends over time
- **Pie Charts**: Distribution of categories
- **Bar Charts**: Comparison of values
- **Line Charts**: Trends and patterns

### Interpreting Results

1. **Check Time Period**: Verify the analysis period
2. **Review Statistics**: Check mean, min, max values
3. **Identify Trends**: Look for increasing/decreasing patterns
4. **Compare Periods**: Compare different time periods if available
5. **Consider Context**: Factor in local conditions

### Exporting Results

- **Screenshots**: Capture charts for reports
- **Data Export**: Download raw data (if available)
- **Report Generation**: Include in comprehensive reports

## Best Practices

1. **Start Simple**: Begin with basic analyses
2. **Verify Area**: Ensure area is correctly selected
3. **Check Coverage**: Verify data availability for your area
4. **Review Results**: Always review results for reasonableness
5. **Document**: Note analysis parameters and dates
6. **Compare**: Run multiple analyses for comprehensive insights

## Performance Considerations

### Processing Time

- **Small Areas**: Usually quick (< 1 minute)
- **Medium Areas**: May take 1-5 minutes
- **Large Areas**: Can take 5-15+ minutes
- **Complex Analyses**: May take longer

### Factors Affecting Speed

- Area size
- Analysis complexity
- Data availability
- Server load
- Internet connection

### Tips for Faster Processing

- Use smaller areas when possible
- Avoid peak usage times
- Check server status
- Be patient for large/complex analyses

## Troubleshooting

### Analysis Failed

**Problem**: Analysis returns an error

**Solutions**:
- Verify area is selected
- Check data availability for area/time
- Try a smaller area
- Check internet connection
- Try again after a moment

### No Results

**Problem**: Analysis completes but shows no data

**Solutions**:
- Verify area is within data coverage
- Check date range is valid
- Ensure area geometry is valid
- Try a different area

### Slow Performance

**Problem**: Analysis takes very long

**Solutions**:
- Reduce area size
- Check server status
- Try during off-peak hours
- Contact support if persistent

### Incorrect Results

**Problem**: Results seem incorrect

**Solutions**:
- Verify area selection
- Check analysis parameters
- Review data source documentation
- Compare with known values
- Contact support with details

## Advanced Usage

### Combining Analyses

Run multiple analyses for comprehensive insights:

1. Run LULC for land cover
2. Run PCP for precipitation
3. Run ETa for water use
4. Compare results
5. Generate combined report

### Time Series Analysis

- Compare different time periods
- Identify seasonal patterns
- Detect trends and anomalies
- Project future scenarios

---

Next: [Reports Guide](reports.md)

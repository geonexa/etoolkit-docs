# Analysis Types

EO-Toolkit supports various analysis types for Earth Observation data. This document details each analysis type.

## Land Use Land Cover (LULC)

### Description

Analyzes land cover types and their distribution within a selected area.

### Data Source

- ESA WorldCover
- MODIS Land Cover
- Dynamic World

### Output

- **Pie Chart**: Distribution of land cover classes
- **Statistics**: Area (hectares) per class
- **Percentages**: Percentage breakdown

### Land Cover Classes

Common classes include:

- Cropland
- Grassland
- Tree Cover
- Shrubland
- Built-up
- Permanent Water Bodies
- Herbaceous Wetland
- Bare/Sparse Vegetation

### Use Cases

- Agricultural planning
- Forest monitoring
- Urban development assessment
- Environmental impact studies
- Land use change detection

### Parameters

- **Area**: Required (polygon geometry)
- **Year**: Optional (for time-specific analysis)

### Example Output

```json
[
  {
    "value": 40,
    "label": "Cropland",
    "area_ha": 669.97,
    "percentage": 46.81
  },
  {
    "value": 30,
    "label": "Grassland",
    "area_ha": 530.2,
    "percentage": 37.05
  }
]
```

## Precipitation Analysis (PCP)

### Description

Analyzes precipitation patterns using CHIRPS data.

### Data Source

CHIRPS (Climate Hazards Group InfraRed Precipitation with Station data)

### Output

- **Time Series**: Annual/monthly precipitation
- **Statistics**: Mean, min, max values
- **Trends**: Precipitation trends over time

### Use Cases

- Drought monitoring
- Water resource planning
- Agricultural planning
- Climate studies
- Flood risk assessment

### Parameters

- **Area**: Required
- **Start Year**: Beginning of analysis period
- **End Year**: End of analysis period
- **Aggregation**: Annual, Monthly, etc.

### Temporal Coverage

- Typically: 1981 to present
- Daily, monthly, annual aggregations
- Near real-time updates

## Actual Evapotranspiration (ETa)

### Description

Analyzes actual evapotranspiration rates using WaPOR data.

### Data Source

WaPOR (Water Productivity Open-access Portal)

### Output

- **Time Series**: ETa values over time
- **Statistics**: Mean, seasonal patterns
- **Trends**: ETa trends

### Use Cases

- Irrigation planning
- Water management
- Agricultural productivity assessment
- Drought monitoring
- Water balance studies

### Parameters

- **Area**: Required
- **Start Year**: Beginning of analysis period
- **End Year**: End of analysis period
- **Aggregation**: Annual, Monthly, etc.

### Temporal Coverage

- Typically: 2009 to present
- 10-day, monthly, annual aggregations
- 250m resolution

## Climate Change Analysis

### Description

Projects future climate scenarios using climate models.

### Scenarios

#### SSP245 (Moderate Emissions)

- Moderate mitigation efforts
- Intermediate warming pathway
- Representative of current policies

#### SSP585 (High Emissions)

- High emissions scenario
- Limited mitigation
- High warming pathway

### Data Source

- NEX-GDDP
- CMIP6 models
- NASA Earth Exchange

### Output

- **Temperature Projections**: Future temperature trends
- **Precipitation Projections**: Future precipitation patterns
- **Trend Analysis**: Long-term trends
- **Comparison**: Scenario comparison

### Use Cases

- Climate adaptation planning
- Risk assessment
- Long-term planning
- Policy development
- Infrastructure planning

### Parameters

- **Area**: Required
- **Scenario**: SSP245 or SSP585
- **Projection Period**: Future years

### Temporal Coverage

- Historical: 1950-2014
- Projections: 2015-2100
- Monthly and annual data

## Water Storage Analysis (GRACE)

### Description

Analyzes terrestrial water storage changes using GRACE data.

### Data Source

- GRACE (2002-2017)
- GRACE-FO (2018-present)

### Output

- **Time Series**: Water storage anomalies
- **Trends**: Long-term trends
- **Seasonal Patterns**: Seasonal variations
- **Anomalies**: Deviations from mean

### Use Cases

- Groundwater monitoring
- Water resource assessment
- Drought analysis
- Hydrological studies
- Water balance analysis

### Parameters

- **Area**: Required
- **Temporal Range**: Available data period

### Temporal Coverage

- GRACE: 2002-2017
- GRACE-FO: 2018-present
- Monthly resolution
- ~300km spatial resolution

## Analysis Workflow

### General Process

1. **Select Area**: Choose area of interest
2. **Choose Analysis**: Select analysis type
3. **Configure Parameters**: Set dates, options
4. **Run Analysis**: Execute analysis
5. **View Results**: Charts and visualizations
6. **Export/Report**: Include in reports

### Performance

- **Small Areas** (< 100 km²): Usually < 1 minute
- **Medium Areas** (100-1000 km²): 1-5 minutes
- **Large Areas** (> 1000 km²): 5-15+ minutes

### Factors Affecting Speed

- Area size
- Temporal range
- Dataset complexity
- Server load
- Network speed

## Combining Analyses

### Multi-Analysis Approach

Run multiple analyses for comprehensive insights:

1. **LULC**: Understand land cover
2. **Precipitation**: Assess water availability
3. **ETa**: Evaluate water use
4. **Climate**: Project future conditions
5. **Water Storage**: Monitor groundwater

### Integrated Reports

Combine results in comprehensive reports:

- Executive summary
- Multiple analysis results
- Cross-analysis insights
- Recommendations
- Visualizations

## Best Practices

1. **Start Simple**: Begin with basic analyses
2. **Verify Area**: Ensure correct area selection
3. **Check Coverage**: Verify data availability
4. **Review Results**: Always review for reasonableness
5. **Document Parameters**: Note analysis settings
6. **Compare Periods**: Compare different time periods
7. **Validate**: Cross-check with known values

## Troubleshooting

### Common Issues

- **No Data**: Area outside coverage
- **Slow Processing**: Large area or complex analysis
- **Errors**: Invalid parameters or geometry
- **Timeout**: Long-running operations

### Solutions

- Verify area within data coverage
- Reduce area size or temporal range
- Check parameter validity
- Retry after delay
- Contact support if persistent

---

Next: [Custom Apps](custom-apps.md)

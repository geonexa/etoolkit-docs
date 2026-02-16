# Reports Guide

EO-Toolkit can generate comprehensive PDF reports combining multiple analyses. This guide covers report generation, content, and usage.

## Overview

Reports provide:

- **Comprehensive Analysis**: Multiple analyses in one document
- **Visualizations**: Charts and maps
- **Data Summary**: Key statistics and findings
- **Professional Format**: PDF format for sharing
- **Customizable**: Theme and project phase selection

## Generating Reports

### Prerequisites

Before generating a report:

1. **Area Required**: Must have a saved area
2. **Completed Analyses**: Area should have completed analyses
3. **Theme Selection**: Choose appropriate theme
4. **Project Phase**: Select project phase

### Step-by-Step Process

1. **Navigate to Reports**:
   - Access through area management
   - Or from analysis results page

2. **Select Area**:
   - Choose area with completed analyses
   - Verify area has analysis results

3. **Fill Report Details**:
   - **Theme**: Select theme (e.g., Agriculture, Water, Climate)
   - **Project Phase**: Select phase (e.g., Planning, Implementation)
   - **Country**: Verify country selection

4. **Generate Report**:
   - Click "Generate Report" button
   - Report generation starts asynchronously
   - Task ID is provided for tracking

5. **Monitor Progress**:
   - Check task status
   - Wait for completion
   - Download when ready

### Report Generation Process

Reports are generated asynchronously using Celery:

1. **Task Creation**: Task is queued
2. **Processing**: Report is generated in background
3. **Completion**: Report is saved
4. **Notification**: Status updates available
5. **Download**: Report available for download

## Report Content

### Standard Sections

Reports typically include:

1. **Cover Page**:
   - Report title
   - Area information
   - Generation date
   - Theme and phase

2. **Executive Summary**:
   - Key findings
   - Overview of analyses
   - Main conclusions

3. **Area Information**:
   - Area name and location
   - Geographic boundaries
   - Map visualization

4. **Analysis Results**:
   - LULC analysis (if available)
   - Precipitation analysis (if available)
   - ETa analysis (if available)
   - Climate projections (if available)
   - Water storage analysis (if available)

5. **Charts and Visualizations**:
   - Pie charts
   - Time series charts
   - Maps and spatial visualizations

6. **Data Tables**:
   - Statistical summaries
   - Area calculations
   - Trend data

7. **Recommendations**:
   - Data recommendations
   - Training materials
   - Case studies
   - Next steps

### Customization Options

- **Theme**: Affects recommendations and focus
- **Project Phase**: Influences content emphasis
- **Country**: Provides country-specific context

## Accessing Reports

### Viewing Report Status

1. Check task status using task ID
2. Status indicators:
   - **Pending**: Report queued
   - **Progress**: Report being generated
   - **Success**: Report ready
   - **Failure**: Error occurred

### Downloading Reports

1. Navigate to report section
2. Find completed report
3. Click download button
4. Save PDF file

### Report Storage

- Reports are stored on the server
- Associated with area and user
- Can be re-downloaded
- Deleted when area is deleted

## Report Management

### Viewing Report History

- Access through area management
- View all reports for an area
- See generation dates
- Check report status

### Deleting Reports

1. Select report from history
2. Click delete button
3. Confirm deletion
4. Report is removed

!!! warning "Deletion Warning"
    Deleting a report is permanent. Download and save important reports before deletion.

### Re-generating Reports

If you need to update a report:

1. Run new analyses if needed
2. Generate new report
3. Old report remains available
4. New report has updated data

## Report Best Practices

### Before Generation

1. **Complete Analyses**: Run all desired analyses first
2. **Verify Results**: Review analysis results
3. **Select Appropriate Theme**: Choose relevant theme
4. **Check Area**: Ensure correct area selected

### After Generation

1. **Review Report**: Check content and formatting
2. **Download**: Save report locally
3. **Share**: Distribute as needed
4. **Archive**: Keep for records

### Report Quality

- **Completeness**: Include all relevant analyses
- **Accuracy**: Verify data and results
- **Clarity**: Ensure clear visualizations
- **Relevance**: Select appropriate theme and phase

## Troubleshooting

### Report Generation Failed

**Problem**: Report generation fails

**Solutions**:
- Check area has completed analyses
- Verify all required fields filled
- Check server status
- Try again after a moment
- Contact support with task ID

### Report Not Appearing

**Problem**: Report doesn't show in list

**Solutions**:
- Check report status
- Refresh the page
- Verify user account
- Check area association
- Contact support

### Report Download Failed

**Problem**: Cannot download report

**Solutions**:
- Check report is complete
- Verify file exists
- Try different browser
- Clear browser cache
- Contact support

### Report Content Issues

**Problem**: Report missing content or errors

**Solutions**:
- Verify analyses completed successfully
- Check area has valid data
- Regenerate report
- Contact support with details

### Slow Generation

**Problem**: Report takes very long

**Solutions**:
- Check server status
- Verify area size
- Be patient for complex reports
- Check task queue status
- Contact support if excessive delay

## Tips

1. **Plan Ahead**: Run analyses before generating report
2. **Save Copies**: Download and archive important reports
3. **Review First**: Check report before sharing
4. **Use Appropriate Theme**: Select relevant theme
5. **Document**: Note report purpose and date
6. **Share Selectively**: Share reports appropriately

## Report Use Cases

### Project Planning

- Initial assessment
- Data collection planning
- Resource allocation

### Monitoring

- Progress tracking
- Change detection
- Performance evaluation

### Reporting

- Stakeholder reports
- Donor reports
- Technical documentation

### Decision Making

- Data-driven decisions
- Policy development
- Resource management

---

This completes the User Guide. For more information, see:
- [Features Documentation](../features/data-catalog.md)
- [API Reference](../api/overview.md)
- [Administration Guide](../administration/admin-panel.md)

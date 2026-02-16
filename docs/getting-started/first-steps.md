# First Steps

Welcome to EO-Toolkit! This guide will help you get started with using the application.

## Accessing the Application

1. Open your web browser
2. Navigate to your EO-Toolkit URL:
   - Development: `http://localhost:8000`
   - Production: `https://etoolkit.terrawatch.net`

## Creating Your Account

### Step 1: Register

1. Click on **Sign Up** or navigate to `/accounts/register/`
2. Fill in the registration form:
   - Username
   - Email address
   - Password (choose a strong password)
   - Confirm password
3. Click **Register**

### Step 2: Email Verification

After registration, you'll be redirected to the email verification page:

1. Click **Send OTP** to receive a verification code
2. Check your email for the 6-digit OTP
3. Enter the OTP in the verification form
4. Click **Verify**

!!! note "OTP Expiration"
    OTP codes expire after a certain time. If expired, click **Send OTP** again to receive a new code.

### Step 3: Login

Once verified:

1. Navigate to the login page (`/accounts/login/`)
2. Enter your username and password
3. Click **Sign In**

## Exploring the Dashboard

After logging in, you'll see the main dashboard with:

- **Navigation Menu**: Access different sections
- **Data Catalog**: Browse available datasets
- **Search**: Find data and resources
- **User Menu**: Access your profile and settings

## Your First Analysis

### Step 1: Create an Area

Before running an analysis, you need to define an area of interest:

1. Navigate to the **Data Map** or **Search** page
2. Use the drawing tools to:
   - **Draw a polygon**: Click on the map to create vertices
   - **Upload a GeoJSON**: Upload a pre-defined area file
3. Give your area a name and select the country
4. Save the area

### Step 2: Browse Data Catalog

1. Go to **Data Catalog** from the navigation menu
2. Browse available datasets:
   - Thematic Data (GEE datasets)
   - GeoServer layers
   - Training materials
   - Case studies
3. Use filters to narrow down:
   - By category
   - By theme
   - By partner/provider
   - By keywords

### Step 3: View Data on Map

1. Click on a dataset from the catalog
2. The dataset details page will open
3. Click **View on Map** to visualize the data
4. The layer will be added to the map viewer

### Step 4: Run an Analysis

1. Select your area from the area selector
2. Choose an analysis type:
   - **LULC**: Land Use Land Cover analysis
   - **PCP**: Precipitation analysis
   - **ETa**: Actual Evapotranspiration
   - **Climate Change**: SSP245 or SSP585 scenarios
   - **GRACE**: Water storage analysis
3. Click **Run Analysis**
4. Wait for the analysis to complete
5. View results in charts and visualizations

### Step 5: Generate a Report

1. After running analyses, go to your saved areas
2. Select an area with completed analyses
3. Click **Generate Report**
4. Fill in:
   - Theme (e.g., Agriculture, Water, Climate)
   - Project Phase
   - Country
5. The report will be generated asynchronously
6. Check the task status and download when ready

## Key Features to Explore

### Data Catalog

- **Thematic Data**: Earth Observation datasets from Google Earth Engine
- **Training Materials**: Educational resources and tutorials
- **Case Studies**: Real-world application examples
- **Custom Apps**: External applications and tools
- **Partners**: Information about data providers

### Area Management

- **Draw Areas**: Use the map drawing tools
- **Upload Areas**: Upload GeoJSON files
- **Manage Areas**: View, edit, and delete saved areas
- **Area List**: Access all your saved areas by country

### Analysis Tools

- **Quick Analysis**: Run analyses directly from the map
- **Time Series**: View temporal data trends
- **Charts**: Interactive visualizations
- **Export**: Download analysis results

## Tips for New Users

1. **Start Small**: Begin with a small area to understand the workflow
2. **Explore Data**: Browse the catalog to see what data is available
3. **Read Descriptions**: Dataset descriptions provide important information
4. **Check Examples**: Review case studies for inspiration
5. **Use Filters**: Filters help find relevant data quickly

## Getting Help

- **FAQs**: Check the [FAQs page](../user-guide/overview.md) for common questions
- **Contact**: Use the Contact Us form for support
- **Documentation**: Refer to the [User Guide](../user-guide/overview.md) for detailed information

## Next Steps

- [User Guide](../user-guide/overview.md) - Comprehensive feature documentation
- [Data Catalog Guide](../user-guide/data-catalog.md) - Detailed catalog usage
- [Analysis Tools](../user-guide/analysis-tools.md) - Advanced analysis features

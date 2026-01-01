# Canberra Public Transport Visualisation Project
*Project Specification & Implementation Documentation*

This document serves as both a specification and implementation record for the Canberra Public Transport analysis project. It defines the technical requirements, documents what has been built, and outlines remaining work to complete the project.

---

## 1. Project Overview

### Goal
The goal of this project is to investigate why public transport use in Canberra is low. The analysis focuses on three measurable components:

1. **Accessibility** – distance from each SA1 (Statistical Area Level 1) to the nearest bus stop
2. **Reliability** – variation in real-time bus arrival performance
3. **Frequency** – service headways by route and time period

### Deliverable
A single-page static website (HTML/CSS/JS) hosted on GitHub Pages that embeds 8 interactive Vega-Lite visualizations. Data processing is performed in Python; processed datasets are stored in the `/data/` directory in the repository.

The page also includes a four-section written report (total 800 words maximum, ~200 words each):
- **Aims** of the project
- **Data** sources used and access/automation approaches
- **Analytical challenges** and tools
- **Conclusions** and policy implications

### Project Status
- **Visualizations**: 6 of 8 complete (Charts 1, 1b, 2, 3, 6, 7 ✓ | Charts 4, 5 pending)
- **Data processing**: 7 of 9 Python scripts functional
- **Write-up**: Placeholder structure complete, content to be written
- **Charts required**: 5-8 charts per marking criteria ✓

---

## 2. Repository Structure

The project uses the following directory structure:

```
root/
├── index.html                      # Main project page
├── Chart_journal_look.css          # Stylesheet
├── PROJECT_SPEC.md                 # This file
├── data/                           # Processed datasets
│   ├── chart1_patronage.csv
│   ├── chart1b_passenger_km.csv
│   ├── chart1b_passenger_km_tidy.csv
│   ├── chart2_sa1_accessibility.csv
│   ├── chart3_sa1_boundaries.geojson
│   ├── chart3_stops_all.geojson
│   ├── chart3_stops_belconnen.geojson
│   ├── chart3_stops_tuggeranong.geojson
│   ├── chart3_stops_gungahlin.geojson
│   ├── chart3_stops_north_canberra.geojson
│   ├── chart3_stops_woden_valley.geojson
│   ├── chart3_stops_south_canberra.geojson
│   ├── chart3_stops_weston_creek.geojson
│   ├── chart3_stops_molonglo.geojson
│   ├── chart3_stops_canberra_east.geojson
│   ├── chart3_stops_uriarra_namadgi.geojson
│   ├── chart4_reliability_weekday.csv        # To be created
│   ├── chart4_reliability_weekend.csv        # To be created
│   ├── chart5_international_comparison.csv   # To be created
│   ├── chart6_headways.csv
│   ├── chart6_hourly_counts.csv
│   ├── chart7_sa1_headway_scatter.csv
│   ├── chart7_sa1_service.csv
│   ├── sa1_headway_access.csv                # Intermediate file
│   ├── sa1_headway_access_long.csv           # Intermediate file
│   ├── sa1_headway_access_wide.csv           # Intermediate file
│   └── raw/                                  # Source data
│       ├── SA1_2021_AUST_SHP_GDA2020/
│       ├── LightRailStops_20251130.geojson
│       ├── bitre-yearbook-2024-05-passengers.xlsx
│       ├── au-australian-capital-territory-transport-canberra-gtfs-936/
│       └── [ABS Census CSV files]
├── Project/
│   └── json/                       # Vega-Lite specifications
│       ├── chart1.json
│       ├── chart1b.json
│       ├── chart2.json
│       ├── chart3_belconnen.json
│       ├── chart3_tuggeranong.json
│       ├── chart3_gungahlin.json
│       ├── chart3_north_canberra.json
│       ├── chart3_woden_valley.json
│       ├── chart3_south_canberra.json
│       ├── chart3_weston_creek.json
│       ├── chart3_molonglo.json
│       ├── chart3_canberra_east.json
│       ├── chart3_uriarra_namadgi.json
│       ├── chart4.json                       # To be created
│       ├── chart5.json                       # To be created
│       ├── chart6.json
│       └── chart7.json
└── scripts/                        # Data processing
    ├── process_sa1_distance.py               # ✓ Functional
    ├── extract_headways_from_gtfs.py         # ✓ Functional
    ├── create_vegalite_map.py                # ✓ Functional
    ├── download_gtfs_static.py               # ✓ Functional
    ├── build_transit_stops.py                # ✓ Functional
    ├── save_busstops.py                      # ✓ Functional
    ├── create_act_map.py                     # ✓ Older version
    ├── get_reliability_data.py               # ⚠️ Incomplete
    └── ml_pt_usage_sa1.py                    # ⚠️ Incomplete (optional)
```

---

## 3. Technology Requirements

### Frontend
- Plain HTML/CSS/JS (no frameworks)
- Single CSS file ([Chart_journal_look.css](Chart_journal_look.css)) for typography, layout, and responsive design
- Vega-Lite visualizations embedded using `vegaEmbed` via CDN:
  - Vega 5.x
  - Vega-Lite 5.x
  - Vega-Embed 6.25.0
- Interactive visualizations embedded directly within the page, not in separate pages

### Data Processing
Python scripts use:
- `pandas` – data manipulation
- `geopandas` – geospatial operations
- `shapely` – geometric calculations
- `requests` – API/HTTP access
- `datetime` – time handling
- `scikit-learn` – machine learning (optional ML component)

Scripts generate reproducible CSV/GeoJSON files for import into the frontend.

### Environment
- Python 3.10 (specified in [environment.yml](environment.yml))
- Conda environment with all dependencies

---

## 4. Data Sources

### 4A. Data Sources Actually Used

#### BITRE Yearbook 2024 (Bureau of Infrastructure and Transport Research Economics)
- **Source**: Australian Government Department of Infrastructure publication
- **File**: `data/raw/bitre-yearbook-2024-05-passengers.xlsx`
- **Tables Used**:
  - **Table 5.5h**: Total public transport patronage (passenger trips in millions) and per-capita usage for Canberra, financial years 1976-77 to 2023-24
  - **Table 5.3h**: Motorised passenger kilometres by transport mode
- **Access**: Public dataset, manually downloaded
- **Outputs**:
  - `data/chart1_patronage.csv`
  - `data/chart1b_passenger_km.csv`
  - `data/chart1b_passenger_km_tidy.csv`
- **Used in**: Charts 1 and 1b

#### ABS Census 2021 Data
- **Source**: Australian Bureau of Statistics Census of Population and Housing
- **Tables**:
  - G01: Basic community profile (population counts)
  - G02: Selected medians and averages
  - G43: Method of travel to work by SA1
  - G46A/B: Census characteristics by SA1
  - G57A/B: Qualifications by SA1
- **Files**: 7 CSV files in `data/raw/`
- **Access**: Downloaded from ABS TableBuilder
- **Processing**: Extracted SA1 population and area data
- **Outputs**: Merged into `data/chart2_sa1_accessibility.csv`
- **Used in**: Charts 2, 3, 7

#### ACT SA1 Boundaries (2021)
- **Source**: Australian Statistical Geography Standard (ASGS) 2021
- **File**: `data/raw/SA1_2021_AUST_SHP_GDA2020/` (shapefile format)
- **CRS**: GDA2020 (EPSG:7844), converted to GDA2020 / MGA Zone 55 (EPSG:7856) for metric calculations
- **Processing**:
  - Filtered to ACT region (SA1 codes starting with '801')
  - Converted to GeoJSON format
  - Merged with Census population data
  - Calculated population density
- **Outputs**: `data/chart3_sa1_boundaries.geojson` (5.8 MB)
- **Used in**: Charts 3, 7

#### Bus Stops & Light Rail Stops
- **Sources**:
  - Light rail: `data/raw/LightRailStops_20251130.geojson`
  - Bus stops: Compiled from ACT open data / GTFS feed
- **Processing**:
  - Combined into single dataset
  - Split into 10 district-specific files for map performance
  - Coordinates in WGS84 (EPSG:4326)
- **Outputs**:
  - `data/chart3_stops_all.geojson` (890 KB)
  - `data/chart3_stops_[district].geojson` (10 district files)
- **Used in**: Charts 2, 3, 7

#### ACT GTFS Static Feed
- **Source**: Transport Canberra / OpenMobilityData
- **File**: `data/raw/au-australian-capital-territory-transport-canberra-gtfs-936/`
- **Contents**: `stop_times.txt`, `trips.txt`, `routes.txt`, `calendar.txt`, `stops.txt`
- **Access**: Public GTFS feed, downloaded via [download_gtfs_static.py](scripts/download_gtfs_static.py)
- **Processing**:
  - Extracted timetabled stop times
  - Calculated mean headways by route and service type
  - Service type definitions:
    - **Weekday Peak**: 07:00–09:00 and 16:00–18:00
    - **Weekday Off-Peak**: 09:00–16:00
    - **Saturday**: All service on Saturday
    - **Sunday**: All service on Sunday
- **Outputs**:
  - `data/chart6_headways.csv` (20 KB, 50+ routes × 4 service types)
  - `data/chart6_hourly_counts.csv` (departure counts by hour)
- **Used in**: Charts 6, 7
- **Automation**: Can be updated by re-running [extract_headways_from_gtfs.py](scripts/extract_headways_from_gtfs.py)

### 4B. Alternative/Future Data Sources

These data sources were identified in the original specification but not yet integrated:

#### ACT GTFS-Realtime API
- **Purpose**: Real-time bus reliability data (delays, actual vs scheduled arrival times)
- **Feed Types**: TripUpdates and/or VehiclePositions
- **Status**: API endpoint needs verification; skeleton script exists
- **Required for**: Chart 4
- **Implementation**: [get_reliability_data.py](scripts/get_reliability_data.py) needs completion
- **Planned outputs**:
  - `data/chart4_reliability_weekday.csv`
  - `data/chart4_reliability_weekend.csv`

#### International Comparison Datasets
- **Sources**:
  - World Bank / International Transport Forum (ITF) for PT mode share statistics
  - GTFS feeds or transit operator websites for frequency/headway data
- **Target cities**: Sydney, Melbourne, Brisbane, Perth, Auckland, Wellington, Vancouver, Portland, Seattle, San Francisco, etc.
- **Status**: Dataset compilation not started
- **Required for**: Chart 5
- **Planned output**: `data/chart5_international_comparison.csv`
- **Fields**: city, country, region, avg_headway_peak_min, pt_mode_share_percent, population, sources

#### Additional ABS Census Tables
- **Tables**:
  - Car ownership by SA1
  - Median age by SA1
  - Income by SA1
- **Purpose**: Additional predictors for optional ML model
- **Status**: Not yet integrated
- **Required for**: Optional ML visualization (Chart 8)

---

## 5. Data Processing Scripts

### Functional Scripts (7)

#### [process_sa1_distance.py](scripts/process_sa1_distance.py)
- **Purpose**: Compute distance from each SA1 centroid to nearest bus stop
- **Inputs**:
  - `data/raw/SA1_2021_AUST_SHP_GDA2020/` (SA1 boundaries shapefile)
  - `data/bus_stops.geojson` (bus stop locations)
  - ABS Census CSV files (population data)
- **Processing**:
  - Filters SA1 boundaries to ACT (SA1 codes starting with '801')
  - Converts CRS to EPSG:7856 (GDA2020 / MGA Zone 55) for metric distance calculations
  - Computes SA1 centroids
  - Calculates Euclidean and walking distance to nearest bus stop (in meters)
  - Merges with population and area data
  - Calculates population density (pop/km²)
- **Outputs**: `data/chart2_sa1_accessibility.csv` (88 KB)
- **Fields**: `sa1_code`, `population`, `area_km2`, `density_pop_per_km2`, `distance_euclidean_m`, `distance_walking_m`, `sa2_name`, `sa3_name`
- **Status**: ✓ Complete and functional

#### [extract_headways_from_gtfs.py](scripts/extract_headways_from_gtfs.py)
- **Purpose**: Extract mean headways by route and service type from GTFS static feed
- **Inputs**: `data/raw/au-australian-capital-territory-transport-canberra-gtfs-936/` (GTFS files)
- **Processing**:
  - Parses `stop_times.txt`, `trips.txt`, `routes.txt`, `calendar.txt`
  - Defines service time windows:
    - Weekday Peak: 07:00–09:00, 16:00–18:00
    - Weekday Off-Peak: 09:00–16:00
    - Saturday: All day
    - Sunday: All day
  - Calculates time gaps between consecutive departures (headways)
  - Computes mean headway per route per service type
- **Outputs**:
  - `data/chart6_headways.csv` (20 KB)
  - `data/chart6_hourly_counts.csv` (815 B)
- **Fields**: `route_id`, `route_short_name`, `route_long_name`, `service_type`, `mean_headway_min`, `departure_count`
- **Status**: ✓ Complete and functional

#### [create_vegalite_map.py](scripts/create_vegalite_map.py)
- **Purpose**: Generate Vega-Lite specifications for 10 district-specific choropleth maps
- **Inputs**:
  - `data/raw/SA1_2021_AUST_SHP_GDA2020/` (SA1 boundaries)
  - ABS Census data (population)
  - Bus and light rail stop locations
- **Processing**:
  - Loads SA1 boundaries and filters to ACT
  - Merges Census population data
  - Calculates population density
  - Creates GeoJSON with properties: SA1 code, SA2 suburb, SA3 district, population, density, area
  - Generates 10 separate Vega-Lite JSON specs, one per district
  - Each spec includes:
    - Choropleth layer (density → color gradient)
    - Bus stops layer (blue circles)
    - Light rail stops layer (gold squares)
    - Interactive tooltips
- **Outputs**:
  - `data/chart3_sa1_boundaries.geojson` (5.8 MB)
  - `Project/json/chart3_[district].json` (10 files)
- **Status**: ✓ Complete and functional

#### [download_gtfs_static.py](scripts/download_gtfs_static.py)
- **Purpose**: Download ACT GTFS static feed from Transport Canberra
- **Processing**: HTTP download and unzip
- **Outputs**: `data/raw/au-australian-capital-territory-transport-canberra-gtfs-936/`
- **Status**: ✓ Complete and functional

#### [build_transit_stops.py](scripts/build_transit_stops.py)
- **Purpose**: Build and organize transit stop data by district
- **Processing**: Filters stop data by district boundaries
- **Outputs**: District-specific stop GeoJSON files
- **Status**: ✓ Complete and functional

#### [save_busstops.py](scripts/save_busstops.py)
- **Purpose**: Save processed bus stop data
- **Status**: ✓ Minimal script (176 bytes), functional

#### [create_act_map.py](scripts/create_act_map.py)
- **Purpose**: Earlier version of map generation script
- **Status**: ✓ Functional but superseded by `create_vegalite_map.py`
- **Note**: Kept for reference

### Incomplete Scripts (2)

#### [get_reliability_data.py](scripts/get_reliability_data.py)
- **Purpose**: Poll ACT GTFS-Realtime API for bus reliability data
- **Status**: ⚠️ Skeleton only with TODO comments
- **Issues**:
  - API endpoint not verified
  - No implementation of polling logic
  - No error handling
  - API authentication requirements undefined
- **Planned functionality**:
  - Poll API at configurable interval (e.g., every 30 seconds)
  - Extract scheduled vs predicted/actual arrival times from TripUpdates
  - Calculate delay: `delay_min = (predicted_time - scheduled_time) / 60`
  - Tag with `day_type` (weekday/weekend)
  - Separate into weekday and weekend datasets
- **Planned outputs**:
  - `data/chart4_reliability_weekday.csv`
  - `data/chart4_reliability_weekend.csv`
- **Fields**: `route_id`, `route_name`, `stop_id`, `scheduled_time`, `predicted_time`, `delay_min`, `date`, `day_type`
- **Required for**: Chart 4

#### [ml_pt_usage_sa1.py](scripts/ml_pt_usage_sa1.py)
- **Purpose**: Build ML model predicting PT usage by SA1
- **Status**: ⚠️ Skeleton with TODO comments, no implementation
- **Planned functionality**:
  - Merge accessibility, demographics, frequency data
  - Standardize features
  - Fit regression or clustering model (scikit-learn)
  - Generate predictions or cluster assignments
- **Planned output**: `data/ml_results.csv`
- **Required for**: Optional Chart 8 (not required for submission)

---

## 6. Visualizations Implemented

Chart presentation order in [index.html](index.html): 1 → 1b → 3 → 2 → 7 → 4 → 5 → 6

### Chart 1: Total Public Transport Patronage ✓
- **Type**: Dual-axis line chart with interactive tooltips
- **Data**: [`data/chart1_patronage.csv`](data/chart1_patronage.csv) (2.5 KB, BITRE Yearbook Table 5.5h)
- **JSON Spec**: [`Project/json/chart1.json`](Project/json/chart1.json)
- **Purpose**: Show long-term trends in Canberra public transport usage (1976-77 to 2023-24)
- **Encodings**:
  - X-axis: Financial year
  - Y-axis (left): Total passenger trips (millions)
  - Y-axis (right): Trips per person
  - Two overlaid line layers with independent scales
- **Interactivity**: Tooltips show year, absolute patronage, per-capita usage
- **Status**: ✓ Complete

### Chart 1b: Motorised Passenger Kilometres by Mode ✓
- **Type**: Grouped/stacked bar chart
- **Data**: [`data/chart1b_passenger_km.csv`](data/chart1b_passenger_km.csv), [`data/chart1b_passenger_km_tidy.csv`](data/chart1b_passenger_km_tidy.csv) (BITRE Table 5.3h)
- **JSON Spec**: [`Project/json/chart1b.json`](Project/json/chart1b.json)
- **Purpose**: Contextualize PT mode share within total motorised passenger travel
- **Encodings**:
  - X-axis: Transport mode (road, rail, air)
  - Y-axis: Passenger kilometres
  - Color: Mode type
- **Interactivity**: Tooltips with formatted values
- **Status**: ✓ Complete

### Chart 2: Population Density vs Distance to Nearest Bus Stop ✓
- **Type**: Interactive scatter plot with regression lines and reference thresholds
- **Data**: [`data/chart2_sa1_accessibility.csv`](data/chart2_sa1_accessibility.csv) (88 KB)
- **JSON Spec**: [`Project/json/chart2.json`](Project/json/chart2.json)
- **Purpose**: Highlight accessibility gaps—identify SA1 areas poorly served by public transport
- **Encodings**:
  - X-axis: Population density (log scale, pop/km²)
  - Y-axis: Distance to nearest bus stop (meters)
  - Size: Population
  - Opacity: Filtered by SA3 district selection
- **Interactivity**:
  - **Distance type selector**: Toggle between Euclidean (straight-line) and walking distance
  - **SA3 district filter**: Highlight specific district, fade others
  - Tooltips: SA1 code, suburb (SA2), district (SA3), population, density, distance
- **Reference lines**:
  - 200m: Minimum accessibility standard
  - 400m: Optimal walkable distance
  - 600m: Acceptable threshold
  - 800m: Maximum acceptable distance
- **Regression layers**: Trend lines for each distance type
- **Status**: ✓ Complete

### Chart 3: Choropleth Maps by District (10 variants) ✓
- **Type**: Interactive geographic choropleth map with point overlays
- **Data**:
  - [`data/chart3_sa1_boundaries.geojson`](data/chart3_sa1_boundaries.geojson) (5.8 MB)
  - [`data/chart3_stops_[district].geojson`](data/) (10 district-specific stop files)
- **JSON Specs**: [`Project/json/chart3_[district].json`](Project/json/) (10 separate map specs)
- **Purpose**: Spatial visualization of population density and transit stop coverage by district
- **Encodings**:
  - **Choropleth layer**: SA1 polygons color-coded by population density
    - Color scale: White (low) → Pink → Red → Dark red (high)
  - **Bus stops layer**: Blue circles
  - **Light rail stops layer**: Gold squares
- **Districts**: Belconnen, Tuggeranong, Gungahlin, North Canberra, Woden Valley, South Canberra, Weston Creek, Molonglo, Canberra East, Uriarra-Namadgi
- **Exclusions**: 11 far-flung SA1 regions (Namadgi, Uriarra, Kowen, Majura) excluded to focus on serviced areas
- **Interactivity**:
  - **District selector dropdown**: Switch between 10 district maps
  - **Hover tooltips**: SA1 regions show suburb (SA2), district (SA3), SA1 code, population, density, area
  - **Hover tooltips**: Stops show stop name and type
  - **Zoom/pan**: Mouse wheel zoom, click-drag pan
- **Projection**: Mercator
- **Status**: ✓ Complete

### Chart 4: Bus Service Reliability Distributions ⚠️
- **Type**: Boxplot or histogram of delays by route
- **Data**:
  - [`data/chart4_reliability_weekday.csv`](data/chart4_reliability_weekday.csv) (to be created)
  - [`data/chart4_reliability_weekend.csv`](data/chart4_reliability_weekend.csv) (to be created)
- **JSON Spec**: [`Project/json/chart4.json`](Project/json/chart4.json) (to be created)
- **Purpose**: Show real-time service reliability variation across routes and day types
- **Planned Encodings**:
  - X-axis: Route ID
  - Y-axis: Delay (minutes)
  - Boxplot showing delay distribution per route
- **Planned Interactivity**:
  - **Day type selector**: Toggle between weekday and weekend data
  - Tooltips: Route, mean delay, median delay, interquartile range
- **Reference lines**: 0 min (on-time), ±5 min acceptable variation
- **Color coding**: Early (negative delay) vs late (positive delay)
- **Status**: ⚠️ PLACEHOLDER—requires GTFS-RT API implementation via [get_reliability_data.py](scripts/get_reliability_data.py)

### Chart 5: International Frequency vs PT Usage ⚠️
- **Type**: Scatter plot with Canberra highlight
- **Data**: [`data/chart5_international_comparison.csv`](data/chart5_international_comparison.csv) (to be created)
- **JSON Spec**: [`Project/json/chart5.json`](Project/json/chart5.json) (to be created)
- **Purpose**: Benchmark Canberra against comparable international cities
- **Planned Encodings**:
  - X-axis: Average peak headway (minutes)
  - Y-axis: PT mode share (%)
  - Point size: City population
  - Color: Region (Oceania, North America, Europe, etc.)
  - Highlight: Canberra with distinct color/shape/label
- **Planned Interactivity**:
  - Tooltips: City, country, headway, mode share, population
  - Regression/loess line showing correlation trend
- **Status**: ⚠️ PLACEHOLDER—requires manual dataset compilation from World Bank, ITF, GTFS feeds, operator websites

### Chart 6: Canberra Service Frequency by Route ✓
- **Type**: Interactive horizontal bar chart with color-coded headway buckets
- **Data**: [`data/chart6_headways.csv`](data/chart6_headways.csv) (20 KB)
- **JSON Spec**: [`Project/json/chart6.json`](Project/json/chart6.json)
- **Purpose**: Show headway variation across all Canberra bus routes
- **Encodings**:
  - X-axis: Mean headway (minutes)
  - Y-axis: Route ID
  - Color: Headway buckets (≤5min, ≤10min, ≤15min, ≤30min, ≤60min, >60min)
- **Interactivity**:
  - **Service type selector**: Dropdown to filter by service type
    - Weekday Peak (7-9am, 4-6pm)
    - Weekday Off-Peak (9am-4pm)
    - Saturday
    - Sunday
  - Tooltips: Route, route name, service type, mean headway, departure count
- **Reference lines**: Vertical lines at 5, 10, 15, 30, 60 minutes
- **Status**: ✓ Complete

### Chart 7: SA1 Service Frequency Accessibility ✓
- **Type**: Interactive choropleth map colored by service quality
- **Data**:
  - [`data/chart7_sa1_headway_scatter.csv`](data/chart7_sa1_headway_scatter.csv) (4.3 MB)
  - [`data/chart7_sa1_service.csv`](data/chart7_sa1_service.csv) (156 KB)
- **JSON Spec**: [`Project/json/chart7.json`](Project/json/chart7.json)
- **Purpose**: Visualize SA1 areas by service frequency accessibility (best headway among nearby stops)
- **Encodings**:
  - SA1 polygons color-coded by best headway within chosen distance threshold
  - Color scale: Green (frequent service) → Yellow → Orange → Red (infrequent)
- **Interactivity**:
  - **Distance threshold selector**: 400m, 600m, 800m
  - **Distance method selector**: Euclidean vs walking distance
  - **Service type selector**: Weekday peak, off-peak, weekend
  - **SA3 district filter**: Highlight specific district
  - Tooltips: SA1 code, suburb, district, best headway, nearest stop
- **Status**: ✓ Complete

---

## 7. Other Chart Ideas

These visualization concepts were explored during project development but not implemented in the final page:

### Original Chart 1 Concept: ABS Method of Travel to Work Time Series
- **Type**: Line or area chart
- **Data**: ABS Census Table G43 (Method of travel to work) for 2011, 2016, 2021
- **Purpose**: Show PT mode share trends over time
- **Why not used**: BITRE patronage data (actual Chart 1) provides longer time series and more granular trends

### ML Predictions Visualization
- **Type**: Scatter plot (actual vs predicted PT usage by SA1)
- **Data**: Output from [ml_pt_usage_sa1.py](scripts/ml_pt_usage_sa1.py) (when implemented)
- **Purpose**: Validate ML model performance, identify over/under-prediction patterns
- **Status**: Optional feature, not required for marking criteria

### SA1 Cluster Map
- **Type**: Choropleth map color-coded by cluster assignment
- **Data**: K-means or hierarchical clustering of SA1s based on accessibility characteristics
- **Purpose**: Identify distinct neighborhood typologies (transit-rich urban, auto-dependent suburban, etc.)
- **Status**: Optional enhancement

### Combined Reliability + Frequency Scatter by Route
- **Type**: Scatter plot (x = headway, y = delay variability)
- **Data**: Merge Chart 4 and Chart 6 data
- **Purpose**: Identify routes with both infrequent service AND poor reliability
- **Status**: Requires Chart 4 data to implement

### Hourly Departure Patterns
- **Type**: Heatmap or line chart
- **Data**: [`data/chart6_hourly_counts.csv`](data/chart6_hourly_counts.csv)
- **Purpose**: Show peak vs off-peak service distribution across hours of day
- **Status**: Data available, visualization not prioritized

---

## 8. Page Layout Requirements

### HTML Structure
The [index.html](index.html) page contains:

1. **Header**:
   - Project title: "Canberra Public Transport: Why is usage so low?"
   - Byline with author name and links (LinkedIn, GitHub)

2. **Four Write-Up Sections** (800 words total, ~200 each):
   - **Aims of the Project**: Research question and approach
   - **Data Sources and Access**: Data used, APIs, automation
   - **Analytical Challenges and Tools**: Technical challenges, methods, tools (Python, geopandas, Vega-Lite)
   - **Conclusions**: Key findings and policy implications
   - **Status**: ⚠️ Placeholder text present, content to be written

3. **Eight Chart Sections** (in presentation order):
   - **Chart 1**: Public Transport Patronage trends
     - Div ID: `#chart1`
     - Caption explaining dual-axis line chart
   - **Chart 1b**: Motorised Passenger Kilometres by Mode
     - Div ID: `#chart1b`
     - Caption explaining BITRE Table 5.3h
   - **Chart 3**: Spatial Distribution (choropleth map, presented 2nd)
     - Div ID: `#chart3`
     - Caption explaining district selector, density color-coding, stop overlays
   - **Chart 2**: Accessibility scatter plot (presented 3rd)
     - Div ID: `#chart2`
     - Caption explaining regression, thresholds, interactivity
   - **Chart 7**: Service Frequency Accessibility (presented 4th)
     - Div ID: `#chart7`
     - Caption explaining service quality mapping
   - **Chart 4**: Reliability distributions (presented 5th)
     - Div ID: `#chart4`
     - Caption explaining delay visualization
     - **Status**: Placeholder chart loaded
   - **Chart 5**: International comparison (presented 6th)
     - Div ID: `#chart5`
     - Caption explaining benchmarking
     - **Status**: Placeholder chart loaded
   - **Chart 6**: Canberra route headways (presented 7th)
     - Div ID: `#chart6`
     - Caption explaining service type selector

4. **Scripts** (loaded at bottom of `<body>`):
   - Vega 5.x (CDN)
   - Vega-Lite 5.x (CDN)
   - Vega-Embed 6.25.0 (CDN)
   - Inline JavaScript for chart embedding

### CSS Styling
[Chart_journal_look.css](Chart_journal_look.css) provides:
- Maximum-width content container (~800px)
- Clean typography (serif for body, sans-serif for headings)
- Responsive chart containers (`.chart-container`)
- Section headers with bottom borders
- Light color theme (white background, black text, accent colors)
- Utility classes (`.big`, `.byline`, `.section-header`)

### Chart Embedding
Charts are embedded using a JavaScript loop:
```javascript
const charts = [
    { id: '#chart1', spec: 'Project/json/chart1.json' },
    { id: '#chart1b', spec: 'Project/json/chart1b.json' },
    { id: '#chart2', spec: 'Project/json/chart2.json' },
    { id: '#chart4', spec: 'Project/json/chart4_placeholder.json' },  // To be replaced
    { id: '#chart5', spec: 'Project/json/chart5_placeholder.json' },  // To be replaced
    { id: '#chart6', spec: 'Project/json/chart6.json' },
    { id: '#chart7', spec: 'Project/json/chart7.json' }
];

charts.forEach(chart => {
    vegaEmbed(chart.id, chart.spec, { actions: false, renderer: 'canvas' });
});
```

**Chart 3** uses custom JavaScript for district selector:
- Dropdown element dynamically created
- User selection triggers re-render of map with selected district spec
- 10 district specs stored in object: `chart3Specs = { belconnen: '...', tuggeranong: '...', ... }`

---

## 9. Marking Criteria Alignment

The project is designed to meet all five marking criteria specified in the assignment brief:

### 1. Open-Data Approach
*Public accessibility, sourcing, technical guidance*

#### How This Project Addresses It:
- **Public accessibility**:
  - ✓ Hosted on GitHub Pages (public URL)
  - ✓ Full source code available in public GitHub repository
  - ✓ Clear navigation guidance (README with setup instructions)
- **Sourcing**:
  - ✓ All data sources documented in this specification (Section 4)
  - ✓ Links to data sources to be included in write-up (Section 2: Data Sources and Access)
  - ✓ Raw data files preserved in `data/raw/` directory
  - ✓ Processed data files with clear naming convention in `data/` directory
- **Technical guidance**:
  - ✓ Python scripts in [`scripts/`](scripts/) directory with clear filenames
  - ✓ This PROJECT_SPEC.md serves as comprehensive codebook
  - ✓ File structure documented (Section 2)
  - ✓ Script functionality documented (Section 5)
  - ✓ APIs used: GTFS Static feed (automated download via [download_gtfs_static.py](scripts/download_gtfs_static.py))
  - ⚠️ GTFS-Realtime API to be implemented for Chart 4

#### Evidence for Graders:
- Python script documentation in Section 5
- Data source documentation in Section 4
- Repository structure in Section 2
- All scripts can be re-run to reproduce datasets

---

### 2. Data Design
*Clarity of question, link between question and datasets, empirical approach*

#### How This Project Addresses It:
- **Clarity of question**:
  - ✓ Clear research question: "Why is public transport use in Canberra low?"
  - ✓ Decomposed into three measurable components: accessibility, reliability, frequency
  - ✓ Each component mapped to specific datasets and visualizations
- **Link between data question and datasets sought out**:
  - ✓ **Accessibility question** → SA1 boundaries + bus stops + geospatial distance calculation → Charts 2, 3
  - ✓ **Frequency question** → GTFS static feed + headway extraction → Charts 6, 7
  - ✓ **Patronage trends question** → BITRE historical data → Charts 1, 1b
  - ✓ **Benchmarking question** → International comparison data → Chart 5
  - ⚠️ **Reliability question** → GTFS-Realtime API (to be implemented) → Chart 4
- **Empirical approach**:
  - ✓ **Data gathering**: Downloaded public datasets (ABS, BITRE, GTFS, geospatial)
  - ✓ **Data cleaning**:
    - Filtered SA1 boundaries to ACT region
    - Converted geospatial CRS for metric calculations
    - Parsed GTFS text files
    - Merged Census data with geometries
    - Calculated derived metrics (density, distance, headway)
  - ✓ **Data analysis**:
    - Geospatial proximity analysis (nearest stop calculation)
    - Time series analysis (patronage trends)
    - Service frequency analysis (headway distributions)
    - Spatial analysis (choropleth mapping)

#### Evidence for Graders:
- Research question stated in Section 1
- Data-to-question mapping in Sections 4 and 6
- Processing methodology in Section 5 (script documentation)
- Visualization design in Section 6

---

### 3. Automation and Replication
*Updateability, use of APIs, scrapers, codebooks*

#### How This Project Addresses It:
- **How easy to update is your project?**
  - ✓ **Very easy**: All data processing is scripted in Python
  - ✓ **7 functional scripts** can be re-run to regenerate datasets
  - ✓ **Modular design**: Each script handles one data processing task
  - ✓ **Clear file paths**: Hardcoded paths in scripts match directory structure
  - ✓ **No manual processing**: All geospatial calculations, GTFS parsing, and data merging automated
- **For you in the future?**
  - ✓ Can update GTFS feed via [download_gtfs_static.py](scripts/download_gtfs_static.py)
  - ✓ Can recalculate headways via [extract_headways_from_gtfs.py](scripts/extract_headways_from_gtfs.py)
  - ✓ Can update Census data by replacing CSV files and re-running [process_sa1_distance.py](scripts/process_sa1_distance.py)
  - ✓ Can regenerate maps via [create_vegalite_map.py](scripts/create_vegalite_map.py)
- **For someone else looking to build on it?**
  - ✓ Comprehensive documentation in this PROJECT_SPEC.md
  - ✓ Clear directory structure (Section 2)
  - ✓ Script functionality explained (Section 5)
  - ✓ Data sources documented with access methods (Section 4)
  - ✓ Vega-Lite JSON specs are human-readable and modular
- **The use of APIs, where suitable**:
  - ✓ **GTFS Static Feed**: Downloaded and parsed programmatically
  - ⚠️ **GTFS-Realtime API**: Planned for Chart 4 (not yet implemented)
- **The use of scrapers, if appropriate, and clear linking to codebooks**:
  - ✓ **No web scraping needed**: All data from official public datasets or APIs
  - ✓ **Codebook**: This PROJECT_SPEC.md serves as comprehensive codebook documenting:
    - Data sources (Section 4)
    - File formats and fields (Section 5)
    - Processing methodology (Section 5)
    - Visualization mappings (Section 6)

#### Evidence for Graders:
- Script documentation in Section 5
- APIs used: GTFS Static (Section 4A), GTFS-Realtime planned (Section 4B)
- This PROJECT_SPEC.md as codebook
- Reproducible pipeline: raw data → scripts → processed data → visualizations

---

### 4. Visual Impact and Grammar of Graphics
*Adherence to good visualization principles, suitability of complex charts, interactivity*

#### How This Project Addresses It:
- **For simple charts, your adherence to the principles of good visualisation**:
  - ✓ **Chart 1 (Line chart)**:
    - Appropriate for time series data
    - Dual-axis clearly labeled
    - Clean design, no chart junk
    - Tooltips for precise values
  - ✓ **Chart 1b (Bar chart)**:
    - Appropriate for categorical comparison
    - Clear axis labels
    - Consistent color scheme
  - ✓ **Chart 6 (Bar chart)**:
    - Horizontal bars for readability with many routes
    - Color-coded headway buckets for quick interpretation
    - Reference lines for context
- **For complex charts, their suitability and impact on the reader**:
  - ✓ **Chart 2 (Scatter with regression)**:
    - Appropriate for correlation analysis
    - Log scale on x-axis handles wide density range
    - Regression lines show trend
    - Reference lines (200m, 400m, 600m, 800m) provide accessibility context
    - Size encoding (population) adds third dimension
  - ✓ **Chart 3 (Choropleth map with overlays)**:
    - Appropriate for spatial distribution
    - Color gradient (white → red) intuitively represents density
    - Multiple layers (polygons + points) show relationships
    - District-specific maps improve readability (avoid overcrowding)
  - ✓ **Chart 7 (Choropleth by service quality)**:
    - Appropriate for spatial service quality analysis
    - Color gradient (green → red) intuitively represents service quality
    - Links accessibility to service frequency
- **The use of interactives to engage the reader**:
  - ✓ **Chart 2**: Distance type selector, SA3 district filter
  - ✓ **Chart 3**: District selector dropdown, hover tooltips, zoom/pan
  - ✓ **Chart 4**: Day type selector (when implemented)
  - ✓ **Chart 5**: Hover tooltips (when implemented)
  - ✓ **Chart 6**: Service type selector (4 options)
  - ✓ **Chart 7**: Multiple selectors (distance threshold, method, service type, district)
  - ✓ **All charts**: Interactive tooltips for data exploration

#### Evidence for Graders:
- Chart designs documented in Section 6
- Appropriate chart types for each data question
- Consistent color schemes across visualizations
- Interactive features enable exploration without overwhelming
- Reference lines and thresholds provide context

---

### 5. Description / Write-Up
*Clarity of discussion, logical steps from question to data to method to conclusion, impact*

#### How This Project Addresses It:
- **Clarity of discussion**:
  - ✓ Four-section structure follows logical flow: Aims → Data → Method → Conclusions
  - ✓ 800-word total limit ensures concise communication
  - ⚠️ **Status**: Placeholder text present, content to be written
- **Logical steps from question to data, to method, to conclusion**:
  - ✓ **Section 1 (Aims)**: Establishes research question (Why is Canberra PT usage low?)
  - ✓ **Section 2 (Data Sources and Access)**: Documents data used to answer question
  - ✓ **Section 3 (Analytical Challenges and Tools)**: Explains processing methodology
  - ✓ **Section 4 (Conclusions)**: Synthesizes findings and policy implications
  - ✓ **Chart captions**: Each visualization has explanatory caption
- **Impact: does the project stimulate further work?**
  - ✓ **Alternative chart ideas** documented (Section 7)
  - ✓ **Future data sources** identified (Section 4B)
  - ✓ **Reproducible methodology** allows extension to other cities
  - ✓ **Clear gaps** (reliability data, international comparison) invite further research
  - ✓ **ML model framework** (skeleton script) provides starting point for predictive modeling

#### Evidence for Graders:
- Four-section write-up structure in [index.html](index.html) (to be populated)
- Logical flow from research question → data → analysis → findings
- Chart captions explain each visualization
- This PROJECT_SPEC.md demonstrates ambition and scope for future work

#### To Be Completed:
- Write 4 × ~200-word sections in [index.html](index.html):
  1. Aims: Research question, three-component approach, significance
  2. Data Sources: List sources (BITRE, ABS, GTFS, SA1 boundaries), access methods, automation (Python scripts, APIs)
  3. Analytical Challenges: Geospatial CRS conversion, GTFS parsing, Census merging, API integration challenges, tools used (Python, pandas, geopandas, Vega-Lite)
  4. Conclusions: Key findings (accessibility gaps, infrequent service, spatial disparities), policy implications (improve stop coverage, increase frequency, target underserved areas)

---

## 10. Outstanding Work to Complete Project

### Priority 1: Required for Submission

#### 1. Write 4 × 200-Word Report Sections
- **File**: [index.html](index.html), sections with class `.section-header`
- **Content**:
  - **Aims** (~200 words):
    - Research question: Why is Canberra PT usage low?
    - Three-component approach: accessibility, reliability, frequency
    - Significance: Policy implications for improving PT uptake
  - **Data Sources and Access** (~200 words):
    - List all sources: BITRE Yearbook, ABS Census, SA1 boundaries, bus stops, GTFS
    - Access methods: Public datasets (manual download), GTFS API (automated)
    - Automation: Python scripts in `/scripts/` directory, reproducible pipeline
    - Hyperlinks to data sources
  - **Analytical Challenges and Tools** (~200 words):
    - Geospatial processing: CRS conversion (EPSG:7856), distance calculations
    - GTFS parsing: Timetable processing, headway calculation
    - Data integration: Merging Census data with geometries
    - Challenges: GTFS-RT API access, international data compilation
    - Tools: Python (pandas, geopandas, shapely), Vega-Lite, GitHub Pages
  - **Conclusions** (~200 words):
    - Key findings: Accessibility gaps in outer suburbs, infrequent service (many routes >30min headway), spatial disparities
    - Implications: Targeted improvements needed in underserved areas, frequency increases on key routes
    - Limitations: Reliability data pending, international comparison incomplete
    - Future work: Complete Charts 4-5, ML model, extend to other cities
- **Estimated time**: 2-3 hours

#### 2. Implement Chart 4: Bus Service Reliability
- **Script**: [scripts/get_reliability_data.py](scripts/get_reliability_data.py)
- **Tasks**:
  1. Verify GTFS-Realtime API endpoint for Transport Canberra
  2. Implement API polling logic (configurable interval, e.g., every 30 seconds)
  3. Parse TripUpdates feed (protobuf or JSON format)
  4. Extract scheduled vs predicted arrival times
  5. Calculate delay: `delay_min = (predicted - scheduled) / 60`
  6. Tag with date and day_type (weekday/weekend)
  7. Run data collection for 1-2 weeks to build representative sample
  8. Output two CSV files:
     - `data/chart4_reliability_weekday.csv`
     - `data/chart4_reliability_weekend.csv`
  9. Create Vega-Lite spec [`Project/json/chart4.json`](Project/json/chart4.json):
     - Boxplot: x = route, y = delay
     - Parameter for day_type filter
     - Color coding: negative (early) vs positive (late) delays
     - Reference lines at 0, ±5 min
     - Tooltips: route, mean/median/range
  10. Test in [index.html](index.html) (replace placeholder reference)
- **Estimated time**: 3-4 hours (excluding data collection wait time)

#### 3. Implement Chart 5: International Frequency vs PT Usage
- **Data compilation**:
  1. Select 10-15 comparable cities:
     - Australian: Sydney, Melbourne, Brisbane, Perth, Adelaide
     - New Zealand: Auckland, Wellington
     - North American: Vancouver, Portland, Seattle, San Francisco, Denver
     - European: Copenhagen, Zurich (if relevant)
  2. Find peak-hour headway data (7-9am typical):
     - GTFS feeds (where available)
     - Transit operator websites
     - Academic papers or transport reports
  3. Find PT mode share data:
     - World Bank Open Data
     - International Transport Forum (ITF) statistics
     - National census data
  4. Document all sources
  5. Create CSV: `data/chart5_international_comparison.csv`
     - Fields: `city`, `country`, `region`, `avg_headway_peak_min`, `pt_mode_share_percent`, `population`, `source_headway`, `source_mode_share`
- **Visualization**:
  1. Create Vega-Lite spec [`Project/json/chart5.json`](Project/json/chart5.json):
     - Scatter plot: x = headway, y = mode share
     - Size: population
     - Color: region
     - Highlight Canberra: different shape/color/annotation
     - Regression or loess line
     - Tooltips: city, country, both metrics
  2. Test in [index.html](index.html) (replace placeholder reference)
- **Estimated time**: 2-4 hours (research + compilation + visualization)

---

### Priority 2: Enhancements (Optional but Recommended)

#### 4. Convert SA1 Boundaries to TopoJSON
- **Purpose**: Reduce file size from 5.8 MB (GeoJSON) to ~1-2 MB (TopoJSON)
- **Tool**: `topojson` command-line tool or Python `topojson` library
- **Tasks**:
  1. Install TopoJSON converter
  2. Convert `data/chart3_sa1_boundaries.geojson` to `data/chart3_sa1_boundaries.topojson`
  3. Update 10 Chart 3 JSON specs to reference `.topojson` file
  4. Test all district maps load correctly
- **Estimated time**: 1 hour

#### 5. Add Error Handling to Python Scripts
- **Purpose**: Robustness for future users
- **Tasks**:
  1. Add try-except blocks for file I/O
  2. Add validation for input data (check columns exist, CRS is correct, etc.)
  3. Add helpful error messages
  4. Add logging for debugging
- **Estimated time**: 1-2 hours

#### 6. Document Data Sources with Hyperlinks in Write-Up
- **Purpose**: Meet "sourcing" criterion explicitly
- **Tasks**:
  1. Add hyperlinks to all data sources in "Data Sources and Access" section
  2. Link to BITRE Yearbook download page
  3. Link to ABS Census TableBuilder
  4. Link to ACT open data portal (SA1 boundaries, stops)
  5. Link to GTFS feed (OpenMobilityData or Transport Canberra)
- **Estimated time**: 30 minutes

---

### Priority 3: Optional Extensions

#### 7. Implement ML Model Visualization (Optional Chart 8)
- **Script**: [scripts/ml_pt_usage_sa1.py](scripts/ml_pt_usage_sa1.py)
- **Tasks**:
  1. Merge accessibility, demographics, frequency data by SA1
  2. Define target variable (PT mode share from ABS Census G43)
  3. Standardize features
  4. Fit regression model (e.g., linear, random forest) or clustering (K-means)
  5. Generate predictions or cluster assignments
  6. Output `data/ml_results.csv`
  7. Create Vega-Lite spec for actual vs predicted scatter or cluster map
  8. Add to [index.html](index.html) as optional Chart 8
- **Estimated time**: 3-4 hours

#### 8. Expand International Comparison
- **Purpose**: More robust benchmarking
- **Tasks**:
  1. Add more cities (target 20-25)
  2. Include European cities for comparison
  3. Add additional metrics (PT investment per capita, network density)
- **Estimated time**: 2-3 hours

---

## 11. Constraint: Use Only Course Techniques

All code generated for this project must follow the methods, patterns, and visualization techniques taught in the course material (Lectures 1–9). The implementation prioritizes the same level of complexity, tools, and approaches demonstrated in those materials. This includes:

- **Vega-Lite for all charting**, following examples shown in lectures
- **Simple data transforms** (filter, calculate, aggregate, lookup, regression) as demonstrated in the course
- **Mapping approach** from the mapping lecture for TopoJSON, projections, and layering
- **Scraping and API methods** consistent with introductory patterns taught in data-access lectures
- **Python control structures** (loops, conditionals, functions) at the level shown in class examples
- **Interactivity** within the bounds of course examples (dropdowns, selections, simple bindings)

**Do not introduce more advanced, unnecessary, or unfamiliar libraries, frameworks, or visualization systems.** Only use more advanced techniques where the project explicitly requires it (e.g., regression transform in Vega-Lite, or basic scikit-learn models for the optional ML task).

The goal is to ensure the entire project is reproducible using the methods expected from the course and understandable to someone grading it against the portfolio and project criteria.

---

## 12. Summary

This project investigates why Canberra's public transport usage is low through a data-driven analysis of accessibility, service frequency, and (planned) reliability. The deliverable is a single-page interactive website with 8 visualizations (6 complete, 2 pending), supported by automated Python data processing scripts and comprehensive documentation.

### Project Strengths:
- ✓ Clear research question decomposed into measurable components
- ✓ Strong geospatial analysis (SA1-level accessibility calculations)
- ✓ Sophisticated interactive visualizations (10 district maps, multi-parameter filters)
- ✓ Automated data pipeline (7 functional scripts)
- ✓ Well-documented codebase (this specification, clear file structure)
- ✓ Meets 5-8 chart requirement (currently 6 complete, will be 8 with Charts 4-5)

### Remaining Work (Priority 1):
1. Write 4 × 200-word report sections (2-3 hours)
2. Implement Chart 4 reliability visualization (3-4 hours + data collection)
3. Implement Chart 5 international comparison (2-4 hours)

**Total remaining effort**: ~10-15 hours to complete submission-ready project.

### GitHub Pages Deployment:
- Host [index.html](index.html) on GitHub Pages
- Ensure all data files and JSON specs are committed to repository
- Test that all charts load correctly from live URL
- Update README with link to live page and setup instructions

---

*Document last updated*: 2025-12-19
*Project status*: ~75% complete (6/8 charts, data pipeline functional, write-up pending)

# SKYTRAX Top 10 Airlines Fleet Analysis 2024

## Overview

A comprehensive analysis of the world's top 10 airlines according to SKYTRAX 2024 rankings, focusing on fleet composition and aircraft valuations. This project combines data engineering and visualisation to provide insights into commercial aviation fleet strategies.

<a href="https://public.tableau.com/app/profile/valerie.madojemu/viz/FleetInformationSkytraxTop10Airlines2024/SkyTraxTop10AirlinesHorizontal">
    <img src="https://public.tableau.com/thumb/views/FleetInformationSkytraxTop10Airlines2024/SkyTraxTop10AirlinesHorizontal" width ="80%">
  </a>
   </a>

## Project Objectives

- Extract and analyse fleet data for SKYTRAX's top 10 ranked airlines
- Create an interactive dashboard for fleet composition analysis
- Evaluate fleet metrics across carriers
- Enable comparative analysis between different airlines

## Technologies Used

- **Data Processing & ETL**: Python 3.9.6
    - BeautifulSoup4 (Web scraping)
    - Pandas (Data manipulation)
    - NumPy (Numerical operations)
- **Data Visualisation**: Tableau
- **Version Control**: Git/GitHub
- **Documentation**: Markdown

# Data Collection & Architecture

## Data Sources

### 1. SKYTRAX World Airline Awards

- Source: [World Airline Awards](https://www.worldairlineawards.com/)
- Data collected: Top 10 airlines ranking for 2024
- Manual collection and verification process
- Forms the foundation table for airline identification

### 2. Planespotters.net
- Source: [Planespotters](https://www.planespotters.net/airline/Qatar-Airways)
- Comprehensive fleet information for each airline
- Data points collected:
    - Aircraft registration numbers
    - Aircraft types and variants
    - Delivery dates and age
    - Current operational status
    - Configuration details

### 3. Axon Aviation
- Source: [Axon Aviation](http://www.axonaviation.com/commercial-aircraft/aircraft-data/aircraft-pricing)
- Aircraft pricing data
- Market valuations for different aircraft types
- Configuration specifications
- Operating cost indicators

## Database Schema

![image 23](https://github.com/user-attachments/assets/56367c6d-abc9-4525-8511-e1c536a850f6)

The project uses a relational database structure with four main tables:

1. **AIRLINE_BASE** (Primary Table)
    - Manually created from SKYTRAX rankings
    - Contains airline identification and alliance information
    - Primary key: Airline_ID_Rank
2. **AIRLINE_DETAILS**
    - Extended airline information
    - Operational data and fleet statistics
    - Foreign key link to AIRLINE_BASE
3. **FLEET_DATA**
    - Individual aircraft information
    - Registration and configuration details
    - Links to both airline and pricing data
4. **AIRCRAFT_PRICING**
    - Aircraft model pricing information
    - Market values in GBP and USD
    - Configuration and specification data

## 

### Web Scraping Implementation

- Custom Python scripts using BeautifulSoup4
- Implemented rate limiting and error handling
- Data validation checks during collection

### Aircraft Type Standardisation

A crucial step in the data preparation was standardising aircraft types to ensure accurate matching between fleet data and pricing information. Here's an example of the standardisation process:

Original Fleet Data variants:

```python

# Before standardisation
"Boeing 777-31HER"  -> "Boeing 777-300ER"
"Boeing 777-3F2ER"  -> "Boeing 777-300ER"
"Boeing 777-36NER"  -> "Boeing 777-300ER"

```

This standardisation was essential for:

- Accurate price matching
- Consistent aircraft family grouping
- Fleet composition analysis
- Cross-airline comparisons

The standardisation rules were implemented through a comprehensive mapping function that considered various aircraft type naming conventions across different data sources.

# ETL Process

## Overview

The ETL pipeline consists of four main Jupyter notebooks, each handling specific aspects of data processing. The process transforms raw HTML and web data into structured, analysable datasets.


## 2. Initial Data Parsing & Cleaning (2_Data_Parsing.ipynb, 2_Data_Cleaning.ipynb)
- [2a: Airline Parsing Table](https://github.com/Mad4Viz/SKYTRAX-Airline-Alliance-and-Fleet-Analysis/blob/main/2_parsed_airline_data.csv)
- [2b: Airline Cleaning Table](https://github.com/Mad4Viz/SKYTRAX-Airline-Alliance-and-Fleet-Analysis/blob/main/2_Data_Cleaning.ipynb)

### Parsing Phase:

- Extracts raw data from HTML structure
- Creates initial DataFrame with 28 columns including:
    - IATA codes
    - Airline details
    - Base/hub information
    - Fleet information
 
  
- Custom `AirlineHTMLParser` class for processing HTML airline tables
- Robust error handling with logging implementation
- Date standardisation to DD/MM/YYYY format

```python
def standardize_date(date_str):
    """Convert date string to DD/MM/YYYY format"""
    try:
        date_obj = datetime.strptime(date_str, '%d %b %Y')
        return date_obj.strftime('%d/%m/%Y')
    except ValueError:
        logging.warning(f"Could not parse date: {date_str}")
        return date_str

```

### Cleaning Phase:

- Consolidates parsed data into structured format
- Implements specific cleaning functions:
    - Callsign consolidation
    - Fleet size standardisation
    - Hub/base information cleaning
    - Automated validation checks

### Example Cleaning Process:

```python
def clean_fleet_size(value):
    """Standardises fleet size format and extracts ordered aircraft"""
    fleet = pd.to_numeric(str(value).split('Aircraft')[0].strip())
    ordered = pd.NA
    if '(' in str(value):
        ordered = pd.to_numeric(
            str(value).split('(')[1].split(')')[0]
            .replace('+ ', '').replace(' On Order/Planned', '')
            .strip()
        )
    return fleet, ordered

```




## 3. Aircraft Fleet Processing (3_Aircraft_Fleet_Data.ipynb)

[3: Airline Aircraft Fleet Table](https://github.com/Mad4Viz/SKYTRAX-Airline-Alliance-and-Fleet-Analysis/blob/main/3_Aircraft_Fleet_Data.ipynb)

### Key Components:

- Aircraft type standardisation
- Dynamic column header detection
- Automated data validation
- Fleet statistics computation

### Data Processing Example:

```python
def standardize_aircraft_type(aircraft_type):
    """Standardises aircraft type based on predefined hierarchical rules"""
    if "Boeing 777-3" in aircraft_type:
        return "Boeing 777-300ER"
    if "Airbus A350-9" in aircraft_type:
        return "Airbus A350-900"

```

## 4. Cost Analysis (4_Airline_Fleet_Cost.ipynb)

[4: Aircraft Cost Data](https://github.com/Mad4Viz/SKYTRAX-Airline-Alliance-and-Fleet-Analysis/blob/main/4_Airline_Fleet_Cost.ipynb)


### Features:

- Currency conversion implementation (USD to GBP)
- Market value calculations
- Price data cleaning and standardisation

## Data Quality Measures

- Extensive error logging and handling
- Data validation at each transformation step
- Cross-reference checks between datasets
- Automated data type verification


# Data Analysis & Visualisation

## Dashboard Overview

An interactive Tableau dashboard was developed to analyse fleet compositions and patterns across the top 10 SKYTRAX-ranked airlines, with a particular focus on understanding the relationship between fleet metrics and rankings.

<a href="https://public.tableau.com/app/profile/valerie.madojemu/viz/FleetInformationSkytraxTop10Airlines2024/SkyTraxTop10AirlinesHorizontal">
    <img src="https://public.tableau.com/thumb/views/FleetInformationSkytraxTop10Airlines2024/SkyTraxTop10AirlinesHorizontal" width ="50%">
  </a>
   </a>

   

## Key Analysis: Qatar Airways - Looking Beyond Fleet Size


### Fleet Metrics Comparison

1. Fleet Size
    - Qatar Airways: 227 aircraft
    - Turkish Airlines: 374 aircraft (64% larger)
    - Emirates: 260 aircraft (14% larger)
2. Fleet Value
    - Qatar Airways: £50.77B
    - Emirates: £73.06B (44% higher)
    - Turkish Airlines: £54.43B (7% higher)

### Quantifiable Insights

1. Fleet Composition & Age
    - Average fleet age: 9.7 years
    - Current fleet size: 227 aircraft
    - Aircraft on order: 11
    - Founded: 22/11/1993 (One of the younger airlines in the top 10)
2. Alliance Membership
    - Member of Oneworld alliance
    - One of three alliance types represented in the top 10

## Interactive Dashboard Features

- Airline selection through parameter actions
- Dynamic fleet composition views
- Age distribution visualisation
- Value comparison capabilities

## Visualisation Components

1. Fleet Size Ranked
    - Comparative bar charts showing total fleet numbers
    - Aircraft type breakdown through Drill-Down
2. Value Analysis
    - Total fleet value comparison
    - Aircraft type value through Drill-Down
    - Individual Aircraft pricing distribution
3. Age Profile
    - Comparative age analysis across airlines

## Key Finding

Our data shows that Qatar Airways, while ranking first in SKYTRAX ratings, maintains neither the largest nor the most valuable fleet among the top 10 airlines. With 227 aircraft and a fleet value of £50.77B, it operates a relatively younger fleet (average age 9.7 years) compared to some competitors. This suggests that fleet size and value alone do not determine an airline's SKYTRAX ranking.



# Results & Business Impact

### 1. Fleet Size vs Rankings

- Turkish Airlines operates the largest fleet (374 aircraft) but ranks 7th
- Qatar Airways ranks 1st with 227 aircraft
- Emirates maintains 3rd position with 260 aircraft

### 2. Fleet Value Distribution

- Emirates leads with highest fleet value: £73.06B
- Notable correlation: Higher fleet values don't directly correspond to higher SKYTRAX rankings
- Investment variations: Fleet values range from £11.42B to £73.06B among top 10

### 3. Fleet Age Analysis

- Average fleet ages range from 7.9 to 12.6 years
- Singapore Airlines (Rank 2) maintains youngest average fleet age at 7.9 years
- Air France (Rank 9) operates oldest average fleet at 12.6 years

### 4. Alliance Patterns

- Star Alliance dominates with 50% representation
- Oneworld accounts for 30% of airlines
- Independent operators like Emirates achieve high rankings without alliance membership

## Future Development Potential

1. Dataset Expansion
    - Incorporate historical fleet data
    - Add aircraft delivery schedules
    - Include additional airline metrics
    - Include airline routes and spatial data
2. Technical Enhancements
    - Automated data updates
    - Additional data validation rules
    - Enhanced visualisations - Airline dedicated dashboards

# Data Quality & Caveats

## Aircraft Pricing Considerations

### 1. Aircraft Variants & Pricing

- Base prices used for aircraft models, but variants may cost more/less
- Custom configurations can significantly impact actual prices
- Engine options affect final pricing
- Additional features and modifications not accounted for

### 2. Commercial Practices

- Bulk purchase discounts not reflected in listed prices
- Second-hand aircraft prices not included in analysis
- Leased vs owned aircraft distinction not available

### 3. Aircraft Type Matching

- Specialty variants may be grouped with base models
- Example: "Boeing 777-31HER", "Boeing 777-3F2ER", "Boeing 777-36NER" all standardised to "Boeing 777-300ER"

### 4. Fleet Data Timing

- Fleet data represents a snapshot in time
- Aircraft orders and deliveries ongoing
- Retirement schedules not included
- Seasonal leasing arrangements not captured

### 5. Value Calculations

- USD to GBP conversion fixed at 0.78 (29th January 2025)
- All fleet data represents a snapshot from 29th January 2025
- Market fluctuations not reflected
- Maintenance status not considered in valuations
- Depreciation rates may vary by airline and usage

### 6. Operational Context

- Route network influence on fleet composition not analysed
- Operational requirements impact on aircraft selection not considered
- Maintenance and operational costs not factored








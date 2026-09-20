# Educational Data Integration and Analysis in Nigeria

This project brings together education data from the United Nations and the World Bank to explore enrolment, adult literacy, and gender inequality in Nigeria.

The work was completed as a group data engineering and analytics project. Each team member handled a different part of the data pipeline, and the cleaned datasets were eventually combined into a single PostgreSQL table for analysis.

The project covers data from 2000 to 2023, although some sources only contain observations for selected years.

## Project Goals

The main goals were to:

- collect education data from different sources
- work with both API and file-based data
- store raw data in MongoDB
- clean and transform the data with Python and Pandas
- store structured datasets in PostgreSQL
- combine the different datasets using country and year
- analyse male and female education indicators
- examine gender gaps in enrolment and literacy
- create clear visualisations of the results

## Data Sources

### United Nations Education Data

The UN dataset was used to analyse Nigerian student enrolment and gross enrolment ratios.

The cleaned data contains:

- primary male gross enrolment ratio
- primary female gross enrolment ratio
- lower secondary male gross enrolment ratio
- lower secondary female gross enrolment ratio
- upper secondary male gross enrolment ratio
- upper secondary female gross enrolment ratio
- total student enrolment by education level

The UN data available for the final comparison contains selected observations for 2005, 2010, 2015 and 2021.

### World Bank Enrolment Data

World Bank enrolment data was collected through the World Bank API.

The indicators used include:

- SE.PRM.ENRR.FE - primary gross enrolment ratio, female
- SE.PRM.ENRR.MA - primary gross enrolment ratio, male
- SE.SEC.ENRR.FE - secondary gross enrolment ratio, female
- SE.SEC.ENRR.MA - secondary gross enrolment ratio, male

The raw API records were stored in MongoDB before being flattened, transformed and written to PostgreSQL.

### World Bank Adult Literacy Data

Adult literacy data was also collected from the World Bank API.

The indicators used were:

- SE.ADT.LITR.FE.ZS - adult female literacy rate
- SE.ADT.LITR.MA.ZS - adult male literacy rate

The literacy data is much more sparse than the enrolment data.

Across 2000 to 2023, the dataset contains only a small number of reported literacy observations. Linear interpolation was therefore used to create a continuous yearly series for analysis.

The original reported values were kept separately from the interpolated values so that the distinction remains clear.

## Team Contributions

### Efe

Efe worked mainly with the United Nations enrolment dataset.

This included:

- cleaning the UN data
- parsing education level and sex from the indicators
- separating percentages from total student enrolment values
- producing a Nigeria-wide dataset
- preparing the UN data for integration with the other sources
- creating UN enrolment visualisations

The final cleaned UN table includes both male and female enrolment ratios across primary, lower secondary and upper secondary education.

### Princewill

Princewill worked with World Bank gender enrolment data.

His pipeline:

1. collected data from the World Bank API
2. stored the raw records in MongoDB
3. flattened the MongoDB records into a Pandas DataFrame
4. labelled each record by education level and sex
5. pivoted the data into male and female enrolment columns
6. calculated gender gaps
7. stored the cleaned result in PostgreSQL

The final PostgreSQL table is:

wb_gender_enrolment

Princewill also created visualisations for:

- primary enrolment by sex
- secondary enrolment by sex
- primary and secondary gender enrolment gaps

### Sayo

Sayo worked with World Bank adult literacy data and the final integration stage.

Her work included:

- collecting male and female literacy data from the World Bank API
- storing raw records in MongoDB
- cleaning the Nigeria records
- identifying reported and missing observations
- applying linear interpolation to missing yearly literacy values
- calculating the male-female literacy gap
- storing the literacy dataset in PostgreSQL
- combining the literacy, World Bank enrolment and UN datasets

The final literacy table is:

gender_literacy_final

The final combined table is:

education_final_merged

## Data Integration

The final stage combines all three parts of the project.

UN Enrolment Data
Efe
        \
         \
          +----> Final Integrated Dataset
         /
World Bank Enrolment
Princewill
         \
          \
           +----> PostgreSQL: education_final_merged
          /
World Bank Literacy
Sayo

The datasets are joined using:

country_code
year

The final integrated dataset contains 24 yearly records covering 2000 to 2023.

After cleaning and validation:

- only Nigeria records are included
- duplicate years = 0
- country code = NGA
- both male and female UN primary enrolment data are available
- World Bank male and female enrolment data are included
- reported and interpolated literacy values are kept separately

## Database Pipeline

The project uses both MongoDB and PostgreSQL.

World Bank API
      |
      v
   MongoDB
  Raw Records
      |
      v
Python / Pandas
Cleaning and Transformation
      |
      v
 PostgreSQL
Structured Tables
      |
      v
Integrated Analysis
      |
      v
Visualisations

MongoDB was useful for storing the raw API responses before transformation.

PostgreSQL was used for the cleaned relational datasets and the final merged table.

## Main PostgreSQL Tables

### wb_gender_enrolment

Contains World Bank enrolment data such as:

- primary female
- primary male
- secondary female
- secondary male
- primary gender gap
- secondary gender gap

### gender_literacy_final

Contains:

- reported female literacy
- interpolated female literacy
- reported male literacy
- interpolated male literacy
- gender literacy gap

### education_final_merged

Contains the final combination of:

- World Bank literacy data
- World Bank enrolment data
- United Nations enrolment data

## Selected Visualisations

### UN Primary Gross Enrolment Ratio by Sex

![UN Primary Enrolment](visualisations/efe_un_primary_enrolment_by_sex.png)

This shows the difference between male and female primary gross enrolment ratios in the selected UN years.

### World Bank Primary Gross Enrolment Ratio by Sex

![World Bank Primary Enrolment](visualisations/prince_primary_enrolment_by_sex_nigeria.png)

This shows the World Bank primary enrolment series for male and female students.

### World Bank Gender Enrolment Gap

![Gender Enrolment Gap](visualisations/prince_gender_enrolment_gap_nigeria.png)

The gender gap is calculated as:

Female enrolment - Male enrolment

Negative values mean male enrolment was higher, while positive values mean female enrolment was higher.

### Female Literacy and Primary Enrolment

![Female Integrated Analysis](visualisations/sayo_female_literacy_un_vs_world_bank_primary_enrolment.png)

This visual combines all three parts of the project:

- Sayo's World Bank literacy data
- Princewill's World Bank enrolment data
- Efe's United Nations enrolment data

### Male Literacy and Primary Enrolment

![Male Integrated Analysis](visualisations/sayo_male_literacy_un_vs_world_bank_primary_enrolment.png)

This provides the same three-source comparison for male education indicators.

### Interpolated Male vs Female Literacy

![Interpolated Literacy](visualisations/sayo_interpolated_male_vs_female_literacy_2000_2023.png)

Because World Bank literacy observations are sparse, the yearly series shown here contains interpolated values.

## Important Data Notes

### Literacy Interpolation

The World Bank literacy dataset does not report a value for every year.

Rather than treating the missing years as reported observations, the project keeps:

- the original literacy value
- an indicator showing whether the value was reported
- a separate interpolated literacy value

This makes it possible to analyse trends while still preserving the original source information.

### 2015 World Bank Primary Enrolment Value

The World Bank primary enrolment series contains a sharp fall in 2015 for both males and females.

This initially looked like a processing error, but the values were checked directly against the World Bank API and are present in the source data.

The value was therefore retained rather than manually changed or removed.

### Gross Enrolment Ratios Can Exceed 100%

Some enrolment ratios in the datasets are above 100%.

This is possible because the indicator is a gross enrolment ratio rather than the percentage of correctly aged children enrolled.

Students who are younger or older than the official age group can therefore cause the ratio to exceed 100%.

## Project Structure

APDV/
│
├── dataset/
│   └── efe_enrolment.csv
│
├── notebooks/
│   ├── efe_apdv_code.ipynb
│   ├── princewill_apdv_code.ipynb
│   └── sayo_apdv_code.ipynb
│
├── visualisations/
│   ├── efe_un_enrolment_by_level_and_sex.png
│   ├── efe_un_primary_enrolment_by_sex.png
│   ├── efe_un_total_enrolment_by_level.png
│   ├── prince_primary_enrolment_by_sex_nigeria.png
│   ├── prince_secondary_enrolment_by_sex_nigeria.png
│   ├── prince_gender_enrolment_gap_nigeria.png
│   ├── sayo_female_literacy_un_vs_world_bank_primary_enrolment.png
│   ├── sayo_male_literacy_un_vs_world_bank_primary_enrolment.png
│   ├── sayo_interpolated_male_vs_female_literacy_2000_2023.png
│   ├── sayo_interpolated_gender_literacy_gap_trend_2000_2023.png
│   ├── sayo_interpolated_gender_literacy_gap_distribution_2000_2023.png
│   └── sayo_literacy_missing_data_heatmap_2000_2023.png
│
├── .gitignore
├── README.md
└── requirements.txt

## Technologies Used

- Python
- Pandas
- NumPy
- Requests
- MongoDB
- PostgreSQL
- SQLAlchemy
- Psycopg2
- Matplotlib
- Seaborn
- Jupyter Notebook
- Docker
- World Bank API

## Running the Project

The notebooks use environment variables for the MongoDB and PostgreSQL connections.

Create a local data.env file in the root project folder.

Example:

DATABASE_USERNAME=your_postgres_username
DATABASE_PASSWORD=your_postgres_password
DATABASE_HOST=localhost
DATABASE_PORT=5432
DATABASE_NAME=apdv_project

MONGO_USERNAME=your_mongodb_username
MONGO_PASSWORD=your_mongodb_password
MONGO_HOST=localhost
MONGO_PORT=27017
MONGO_DB=apdv_project
MONGO_COLLECTION=world_bank_enrolment_raw

The data.env file is excluded from Git through .gitignore and should not be committed to the repository.

Install the required Python packages:

pip install -r requirements.txt

MongoDB and PostgreSQL must also be running before executing the database sections of the notebooks.

The notebooks can then be run from the notebooks folder.

## What This Project Demonstrates

This project goes beyond a single CSV analysis.

It demonstrates:

- API data collection
- file-based data cleaning
- NoSQL data storage
- relational database design
- ETL workflows
- data integration from multiple sources
- missing-data handling
- gender-gap analysis
- data validation
- Python visualisation
- working with real-world data quality problems

The final result is a single education dataset that brings together enrolment and literacy information from two major international data sources and allows male and female education outcomes in Nigeria to be compared over time.
# FitBit_Tracker_Data_Analytics_Practice

## Busines T**ask**:
Analyze the fitibit data to identify trends and find patterns that could help drive a marketing strategy

## Preparation. Description of the data sources:
Furberg, R., Brinton, J., Keating, M., & Ortiz, A. (2016). Crowd-sourced Fitbit datasets 03.12.2016-05.12.2016 [Data set]. Zenodo. https://doi.org/10.5281/zenodo.53894 and it is available in zenodo.

The main takeaways from this **preparation** step:
#### Data organization and structure
- The data is mostly in long format, it repeats the User Id. Each row is a user Id with its respective time, and subsequent columns give information about calories, heart-rate, or exercise intensity.
- The data is reported in different time resolutions: heart rate data is recorded every 5 seconds, intensity, calories and steps are recorded every minute, and sleep data is reported on a daily basis.
  - The minutes-level data from steps, excercise intensity and calories is summarized into hourly data and then into daily activity data.
- Minute data is also reported in wide format, where each row is an hour by user and each column is the data at a given minute. Since this makes the data rectangular, hours where not all minutes were reported are missing.

#### Data credibility. Does the data ROCCC?
- The data is not complete as presented on the kaggle website. It is half of the data: the data from 3.11.2016-4.11.2016 is missing. Nevertheless, it has been used broadly and it is vetted. The first half of the data, contains two users not present in the kaggle website and will be added for the analysis.
- The original source is from the 2016 publication, going to the original zenodo source makes it clear that we are only dealing with half the data set.
- The data is not comprehensive, it does not contain any metadata. The units for distance are not specified, and it is not described whether they were calculated from steps or from GPS. We do not know how is "Intensity" defined. For this information, you must go to the original publication or to the fitbit website, and even then it is not straightforward.
- The data is not so current anymore, it is from 2016, but it could still give some insights. You would have to have in mind that these are 2016 trends in 2023.
- There are no missing values as verified by skim_without_chart. The data was cleaned in the original study.
- The data is cited, it is part from an academic study.

#### Licensing, privacy, security and accessibility:
The data is in the public domain with a CC0 license and it is secure and accessible in the kaggle platform. The users have an anonymized ID and no personal identifiable information is available for them. This is a feature for privacy but it can bias our analysis, since the business task asks us to evaluate usage trends for a company that markets fitness devices to women. We have no information on the gender of people in this dataset.

#### Integrity and problems with the data:
- Verification of the data integrity was done by going to the original source, downloading and then exploring the file contents. The files were not corrupted.
- It is split in half.
- It is redundant, and not well documented.
- **Correction**: the metadata is available [here](https://www.fitabase.com/media/1930/fitabasedatadictionary102320.pdf)


Here is a notebook detailing the structure of the different tables and preliminary exploration. It contains only the second half of the data present in the kaggle website:

[Data loading, sorting, and filtering](https://github.com/jerolon/FitBit_Tracker_Data_Analytics_Practice/blob/main/0_Fit_Bit_Tracker_Data_Sorting_and_Filtering.md)

## Data cleaning
We have 35 unique Ids, which is a minimum sample size. 

[Data cleaning documentation](https://github.com/jerolon/FitBit_Tracker_Data_Analytics_Practice/blob/main/1_Data_Cleaning_and_manipulation.md)

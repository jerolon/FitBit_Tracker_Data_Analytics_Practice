Fit Bit Tracker Data Sorting and Filtering
================
Jeronimo Miranda
2023-05-05

## Loading and exploring the data

We are using the data from
<https://www.kaggle.com/datasets/arashnic/fitbit>. Assume the path to
the data is in a folder above where the repository is. This is an
exploration of the data’s quirks.

### Daily activity data

I am loading the Ids as character because summary statistics like mean
or median are irrelevant whereas string comparisons like “do all Ids
have the same amount of characters” and “how many are unique” are
important to know for knowing about the data integrity.

``` r
data_path <- "../Fitabase Data 4.12.16-5.12.16/"
dailyActivity <- read_csv(paste0(data_path,"dailyActivity_merged.csv"), 
    col_types = cols(Id = col_character(), 
        ActivityDate = col_date(format = "%m/%d/%Y")))
skim_without_charts(dailyActivity)
```

|                                                  |               |
|:-------------------------------------------------|:--------------|
| Name                                             | dailyActivity |
| Number of rows                                   | 940           |
| Number of columns                                | 15            |
| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_   |               |
| Column type frequency:                           |               |
| character                                        | 1             |
| Date                                             | 1             |
| numeric                                          | 13            |
| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ |               |
| Group variables                                  | None          |

Data summary

**Variable type: character**

| skim_variable | n_missing | complete_rate | min | max | empty | n_unique | whitespace |
|:--------------|----------:|--------------:|----:|----:|------:|---------:|-----------:|
| Id            |         0 |             1 |  10 |  10 |     0 |       33 |          0 |

**Variable type: Date**

| skim_variable | n_missing | complete_rate | min        | max        | median     | n_unique |
|:--------------|----------:|--------------:|:-----------|:-----------|:-----------|---------:|
| ActivityDate  |         0 |             1 | 2016-04-12 | 2016-05-12 | 2016-04-26 |       31 |

**Variable type: numeric**

| skim_variable            | n_missing | complete_rate |    mean |      sd |  p0 |     p25 |     p50 |      p75 |     p100 |
|:-------------------------|----------:|--------------:|--------:|--------:|----:|--------:|--------:|---------:|---------:|
| TotalSteps               |         0 |             1 | 7637.91 | 5087.15 |   0 | 3789.75 | 7405.50 | 10727.00 | 36019.00 |
| TotalDistance            |         0 |             1 |    5.49 |    3.92 |   0 |    2.62 |    5.24 |     7.71 |    28.03 |
| TrackerDistance          |         0 |             1 |    5.48 |    3.91 |   0 |    2.62 |    5.24 |     7.71 |    28.03 |
| LoggedActivitiesDistance |         0 |             1 |    0.11 |    0.62 |   0 |    0.00 |    0.00 |     0.00 |     4.94 |
| VeryActiveDistance       |         0 |             1 |    1.50 |    2.66 |   0 |    0.00 |    0.21 |     2.05 |    21.92 |
| ModeratelyActiveDistance |         0 |             1 |    0.57 |    0.88 |   0 |    0.00 |    0.24 |     0.80 |     6.48 |
| LightActiveDistance      |         0 |             1 |    3.34 |    2.04 |   0 |    1.95 |    3.36 |     4.78 |    10.71 |
| SedentaryActiveDistance  |         0 |             1 |    0.00 |    0.01 |   0 |    0.00 |    0.00 |     0.00 |     0.11 |
| VeryActiveMinutes        |         0 |             1 |   21.16 |   32.84 |   0 |    0.00 |    4.00 |    32.00 |   210.00 |
| FairlyActiveMinutes      |         0 |             1 |   13.56 |   19.99 |   0 |    0.00 |    6.00 |    19.00 |   143.00 |
| LightlyActiveMinutes     |         0 |             1 |  192.81 |  109.17 |   0 |  127.00 |  199.00 |   264.00 |   518.00 |
| SedentaryMinutes         |         0 |             1 |  991.21 |  301.27 |   0 |  729.75 | 1057.50 |  1229.50 |  1440.00 |
| Calories                 |         0 |             1 | 2303.61 |  718.17 |   0 | 1828.50 | 2134.00 |  2793.25 |  4900.00 |

Loaded the dates in the correct format. Since apart from date and Id all
columns are numeric, it makes sense to use skim_without_charts which
gives a statistical summary of each variable. The function output is
rendered as an html table, which is quite nice.

What we can see from this summary:

- 33 unique Ids, all have 10 characters.
- Data was collected for 31 days, from april 12 to may 12.
- The minimum for **all** numeric variables is 0. This suggests empty
  days, maybe just days that people did not wear their devices. These
  rows should be eliminated.
- Column names do not need to be cleaned
- The data is in long format
- This data can help answer our question by telling us how frequently
  people track different activities. Before any analysis we can see that
  most activity is done via trackerDistance rather than LoggedActivities

Total distance is not the sum of TrackerDistance and
LoggedActivitiesDistance, as one could assume. For most records,
TotalDistance and TrackerDistance are the same, thought there are
exceptions. Rather it seems to be the sum of the ActiveDistances
columns. Lack of metadata is killing me here.

``` r
filter(dailyActivity, TotalDistance != (LoggedActivitiesDistance + TrackerDistance))
```

    ## # A tibble: 32 x 15
    ##    Id         Activity~1 Total~2 Total~3 Track~4 Logge~5 VeryA~6 Moder~7 Light~8
    ##    <chr>      <date>       <dbl>   <dbl>   <dbl>   <dbl>   <dbl>   <dbl>   <dbl>
    ##  1 6775888955 2016-04-26    7091    5.27    5.27    1.96    3.48   0.870   0.730
    ##  2 6962181067 2016-04-21   11835    9.71    7.88    4.08    3.99   2.10    3.51 
    ##  3 6962181067 2016-04-25   13239    9.27    9.08    2.79    3.02   1.68    4.46 
    ##  4 6962181067 2016-05-09   12342    8.72    8.68    3.17    3.90   1.18    3.65 
    ##  5 7007744171 2016-04-12   14172   10.3     9.48    4.87    4.5    0.380   5.41 
    ##  6 7007744171 2016-04-13   12862    9.65    8.60    4.85    4.61   0.560   4.48 
    ##  7 7007744171 2016-04-14   11179    8.24    7.48    3.29    2.95   0.340   4.96 
    ##  8 7007744171 2016-04-18   14816   11.0     9.91    4.93    3.79   2.12    5.05 
    ##  9 7007744171 2016-04-19   14194   10.5     9.5     4.94    4.41   0.760   5.31 
    ## 10 7007744171 2016-04-20   15566   11.3    10.4     4.92    4.79   0.670   5.86 
    ## # ... with 22 more rows, 6 more variables: SedentaryActiveDistance <dbl>,
    ## #   VeryActiveMinutes <dbl>, FairlyActiveMinutes <dbl>,
    ## #   LightlyActiveMinutes <dbl>, SedentaryMinutes <dbl>, Calories <dbl>, and
    ## #   abbreviated variable names 1: ActivityDate, 2: TotalSteps,
    ## #   3: TotalDistance, 4: TrackerDistance, 5: LoggedActivitiesDistance,
    ## #   6: VeryActiveDistance, 7: ModeratelyActiveDistance, 8: LightActiveDistance

#### Number of days tracked

We group the days by Id and then plot a histogram of how many days
people in the dataset were tracked
![](0_Fit_Bit_Tracker_Data_Sorting_and_Filtering_files/figure-gfm/number%20of%20days%20tracked-1.png)<!-- -->

User **4057192912** is an outlier, having data only for four days.

Running `select(dailyActivity, Id, ActivityDate) %>% distinct()` shows
that there are no duplicate dates for any Id.

Data seems very redundant. The files `dailyCalories_merged.csv`,
`daily_Intensities_merged` and `dailySteps_merged.csv` are just column
subsets of `dailyActivity_merged.csv`

### Heartrate seconds data

``` r
heartrate_seconds <- read_csv(paste0(data_path,"heartrate_seconds_merged.csv"), 
    col_types = cols(Id = col_character(), Time = col_datetime(format = "%m/%d/%Y %H:%M:%S %p"), Value = col_double()))
## Splitting the date time column into date and time
heartrate_date_time <- transmute(heartrate_seconds, Id, date_time = Time, Value, hour_of_day = hms::as_hms(date_time), dia = date(date_time))
skim_without_charts(heartrate_date_time)
```

|                                                  |                     |
|:-------------------------------------------------|:--------------------|
| Name                                             | heartrate_date_time |
| Number of rows                                   | 2483658             |
| Number of columns                                | 5                   |
| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_   |                     |
| Column type frequency:                           |                     |
| character                                        | 1                   |
| Date                                             | 1                   |
| difftime                                         | 1                   |
| numeric                                          | 1                   |
| POSIXct                                          | 1                   |
| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ |                     |
| Group variables                                  | None                |

Data summary

**Variable type: character**

| skim_variable | n_missing | complete_rate | min | max | empty | n_unique | whitespace |
|:--------------|----------:|--------------:|----:|----:|------:|---------:|-----------:|
| Id            |         0 |             1 |  10 |  10 |     0 |       14 |          0 |

**Variable type: Date**

| skim_variable | n_missing | complete_rate | min        | max        | median     | n_unique |
|:--------------|----------:|--------------:|:-----------|:-----------|:-----------|---------:|
| dia           |         0 |             1 | 2016-04-12 | 2016-05-12 | 2016-04-26 |       31 |

**Variable type: difftime**

| skim_variable | n_missing | complete_rate | min    | max        | median     | n_unique |
|:--------------|----------:|--------------:|:-------|:-----------|:-----------|---------:|
| hour_of_day   |         0 |             1 | 0 secs | 86398 secs | 47690 secs |    86046 |

**Variable type: numeric**

| skim_variable | n_missing | complete_rate |  mean |   sd |  p0 | p25 | p50 | p75 | p100 |
|:--------------|----------:|--------------:|------:|-----:|----:|----:|----:|----:|-----:|
| Value         |         0 |             1 | 77.33 | 19.4 |  36 |  63 |  73 |  88 |  203 |

**Variable type: POSIXct**

| skim_variable | n_missing | complete_rate | min        | max                 | median              | n_unique |
|:--------------|----------:|--------------:|:-----------|:--------------------|:--------------------|---------:|
| date_time     |         0 |             1 | 2016-04-12 | 2016-05-12 16:20:00 | 2016-04-26 20:28:50 |   961274 |

Only 14 out of 33 volunteers use a tracker that gets heart-rate data

These graphics span 24 hour periods. So you can see that only 3 people
do not use the device to track sleep. The data is smoothed for every
user, so day to day variability is lost. Still you can see some patterns
of when people tend to exercise more consistently. Later we can average
by the heart rate in sliding windows to plot different days for each
user. It is just that the raw data every 5 seconds is way too dense to
be visualized directly, even for a single day for a single user.

    ## `geom_smooth()` using method = 'gam' and formula = 'y ~ s(x, bs = "cs")'

![](0_Fit_Bit_Tracker_Data_Sorting_and_Filtering_files/figure-gfm/graph%20of%20heart%20rates-1.png)<!-- -->

### Loading and checking hourly data

Finally figured out how to parse date times from readr. There are three
files with hourly aggregated data. Skim without chart is done after
merging all three files.

``` r
hourlyCalories <- read_csv(paste0(data_path, "hourlyCalories_merged.csv"), 
    col_types = cols(Id = col_character(), 
        ActivityHour = col_datetime(format = "%m/%d/%Y %H:%M:%S %p")))

hourlyIntensities <- read_csv(paste0(data_path, "hourlyIntensities_merged.csv"), 
    col_types = cols(Id = col_character(), 
        ActivityHour = col_datetime(format = "%m/%d/%Y %H:%M:%S %p")))

hourlySteps <- read_csv(paste0(data_path, "hourlySteps_merged.csv"), 
    col_types = cols(Id = col_character(), 
        ActivityHour = col_datetime(format = "%m/%d/%Y %H:%M:%S %p")))
hourlyActivity <- inner_join(hourlyCalories, hourlyIntensities, by = c("Id","ActivityHour")) %>% inner_join(hourlySteps, by = c("Id", "ActivityHour"))

skim_without_charts(hourlyActivity)
```

|                                                  |                |
|:-------------------------------------------------|:---------------|
| Name                                             | hourlyActivity |
| Number of rows                                   | 22099          |
| Number of columns                                | 6              |
| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_   |                |
| Column type frequency:                           |                |
| character                                        | 1              |
| numeric                                          | 4              |
| POSIXct                                          | 1              |
| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ |                |
| Group variables                                  | None           |

Data summary

**Variable type: character**

| skim_variable | n_missing | complete_rate | min | max | empty | n_unique | whitespace |
|:--------------|----------:|--------------:|----:|----:|------:|---------:|-----------:|
| Id            |         0 |             1 |  10 |  10 |     0 |       33 |          0 |

**Variable type: numeric**

| skim_variable    | n_missing | complete_rate |   mean |     sd |  p0 | p25 |   p50 |    p75 |  p100 |
|:-----------------|----------:|--------------:|-------:|-------:|----:|----:|------:|-------:|------:|
| Calories         |         0 |             1 |  97.39 |  60.70 |  42 |  63 | 83.00 | 108.00 |   948 |
| TotalIntensity   |         0 |             1 |  12.04 |  21.13 |   0 |   0 |  3.00 |  16.00 |   180 |
| AverageIntensity |         0 |             1 |   0.20 |   0.35 |   0 |   0 |  0.05 |   0.27 |     3 |
| StepTotal        |         0 |             1 | 320.17 | 690.38 |   0 |   0 | 40.00 | 357.00 | 10554 |

**Variable type: POSIXct**

| skim_variable | n_missing | complete_rate | min        | max                 | median              | n_unique |
|:--------------|----------:|--------------:|:-----------|:--------------------|:--------------------|---------:|
| ActivityHour  |         0 |             1 | 2016-04-12 | 2016-05-12 15:00:00 | 2016-04-26 06:00:00 |      736 |

- All three datasets are complete, the joins by Id and hourlyActivity
  conserve the 22099 hours.
- What is Intensity? How is it defined and why does it not appear in
  dailyActivity? Maybe it is some function of hear-rate and steps.

There is data for 24 hours most days. Does this mean that people almost
never take off their devices? Or just that there is hourly data
regardless of whether people wear their fitbits?

``` r
hourlyActivity %>% mutate(fecha = date(ActivityHour)) %>% group_by(fecha, Id) %>% summarise(hoursTracked = n()) %>% ungroup() %>% group_by(hoursTracked) %>% summarise(days = n()) %>% arrange(desc(hoursTracked))
```

    ## # A tibble: 14 x 2
    ##    hoursTracked  days
    ##           <int> <int>
    ##  1           24   903
    ##  2           23     1
    ##  3           22     1
    ##  4           21     1
    ##  5           17     1
    ##  6           16     8
    ##  7           15     6
    ##  8           13     1
    ##  9           12     5
    ## 10           11     2
    ## 11           10     2
    ## 12            6     1
    ## 13            4     1
    ## 14            1     1

Take the intervals between rows. All NAs are when users Id change. All
other gaps are one hour long. Hard to think that users wore their
fitbits for two months continuously. Heart rate data can be used in the
analysis section to answer this question.

``` r
hourlyActivity %>% select(Id, ActivityHour) %>%
  mutate(fecha = date(ActivityHour)) %>% 
  group_by(Id) %>% 
  mutate(gap = ActivityHour - lag(ActivityHour)) %>% ungroup() %>%  group_by(gap) %>% summarise(gaps = n())
```

    ## # A tibble: 2 x 2
    ##   gap       gaps
    ##   <drtn>   <int>
    ## 1  1 hours 22066
    ## 2 NA hours    33

Last, a summary table looking at when the tracking data begins and ends
for each user: the beginning is the same for all: 12 am on april 12.
This suggests that the data was cut to give them a consistent start
date, because users did not just start using their tracker on midnight
the same day. The end times are variable, even when most data ends on
the same day.

``` r
hourlyActivity %>% group_by(Id) %>% summarise(beginning_of_tracking = min(ActivityHour), end_of_tracking = max(ActivityHour))
```

    ## # A tibble: 33 x 3
    ##    Id         beginning_of_tracking end_of_tracking    
    ##    <chr>      <dttm>                <dttm>             
    ##  1 1503960366 2016-04-12 00:00:00   2016-05-11 20:00:00
    ##  2 1624580081 2016-04-12 00:00:00   2016-05-12 15:00:00
    ##  3 1644430081 2016-04-12 00:00:00   2016-05-11 11:00:00
    ##  4 1844505072 2016-04-12 00:00:00   2016-05-12 10:00:00
    ##  5 1927972279 2016-04-12 00:00:00   2016-05-12 15:00:00
    ##  6 2022484408 2016-04-12 00:00:00   2016-05-12 15:00:00
    ##  7 2026352035 2016-04-12 00:00:00   2016-05-12 15:00:00
    ##  8 2320127002 2016-04-12 00:00:00   2016-05-12 14:00:00
    ##  9 2347167796 2016-04-12 00:00:00   2016-04-29 05:00:00
    ## 10 2873212765 2016-04-12 00:00:00   2016-05-12 15:00:00
    ## # ... with 23 more rows

### Loading minute data

Minute data has the peculiarity that each feature is present in **Wide**
and **Narrow** modalities. My first guess was *Wide* meant that each
user had its own column, but it is actually that files have a row per
hour and each column is a minute. I do not understand the rationality
behind this, but wide formats are often useful for visualizations. In
any case, it is a good opportunity to use the package called
“lubridate”.

The features present with minute resolution are:

- Calories
- Intensities
- Steps
- METs (Only present in narrow format God knows why)
- Sleep (Not specified whether it is narrow or wide format)

#### Calories by the minute

``` r
minuteCaloriesNarrow <- read_csv(paste0(data_path, "minuteCaloriesNarrow_merged.csv"), 
    col_types = cols(Id = col_character(), 
        ActivityMinute = col_datetime(format = "%m/%d/%Y %H:%M:%S %p")))

minuteCaloriesWide <- read_csv(paste0(data_path,"minuteCaloriesWide_merged.csv"), 
    col_types = cols(Id = col_character(), 
        ActivityHour = col_datetime(format = "%m/%d/%Y %H:%M:%S %p")))

#This Transforms the wide data to narrow
minuteCalWide_toNarrow <- minuteCaloriesWide %>% pivot_longer(names_to = "Minute", values_to = "Calories", cols = starts_with("Calories"), names_transform = readr::parse_number)

#Note the "minutes" function when adding to ActivityHour, otherwise the numeric column would get added as seconds
minuteCalWide_toNarrow <- minuteCalWide_toNarrow %>% transmute(Id, ActivityMinute = ActivityHour + minutes(Minute), Calories) %>% arrange(Id)
```

#### Discrepancies between the files

Already the number of rows differs between both files

``` r
nrow(minuteCaloriesNarrow) - (60 * nrow(minuteCaloriesWide))
```

    ## [1] 26880

The minuteCaloriesNarrow file and the minuteCaloriesNarrow differ in
data, for some random reason.

We will just make a new minuteCalories object as the intersection of
both files, the Wide file previously transformed to long format in a
chunk above. Delete the intermediate files to save memory

``` r
minuteCalories <- union(minuteCaloriesNarrow, minuteCalWide_toNarrow)
rm(minuteCaloriesNarrow)
rm(minuteCalWide_toNarrow)
```

Now we will compare these data with the previously loaded hourly data.
In the code below, note the combination of the functions `hours` which
transforms a number to hours and `hour` which extracts the hour info
from a date time.

``` r
minuteCalories 
```

    ## # A tibble: 1,346,220 x 3
    ##    Id         ActivityMinute      Calories
    ##    <chr>      <dttm>                 <dbl>
    ##  1 1503960366 2016-04-12 00:00:00    0.786
    ##  2 1503960366 2016-04-12 00:01:00    0.786
    ##  3 1503960366 2016-04-12 00:02:00    0.786
    ##  4 1503960366 2016-04-12 00:03:00    0.786
    ##  5 1503960366 2016-04-12 00:04:00    0.786
    ##  6 1503960366 2016-04-12 00:05:00    0.944
    ##  7 1503960366 2016-04-12 00:06:00    0.944
    ##  8 1503960366 2016-04-12 00:07:00    0.944
    ##  9 1503960366 2016-04-12 00:08:00    0.944
    ## 10 1503960366 2016-04-12 00:09:00    0.944
    ## # ... with 1,346,210 more rows

``` r
#Group the minuteCalories by hour and sum the cals. A column in the summarise function lets us check that we are not including hours where not all minutes are present
minuteCalories2hour <- minuteCalories %>% transmute(Id, ActivityHour = date(ActivityMinute) + hours(hour(ActivityMinute)), Calories) %>%
group_by(Id, ActivityHour) %>% 
summarise(Calories = round(sum(Calories)), n_minutes = n())

minuteCalories2hour <- minuteCalories2hour %>% filter(n_minutes == 60) %>% select(-n_minutes)
setdiff(minuteCalories2hour, hourlyCalories)
```

    ## # A tibble: 338 x 3
    ## # Groups:   Id [19]
    ##    Id         ActivityHour        Calories
    ##    <chr>      <dttm>                 <dbl>
    ##  1 1503960366 2016-05-11 21:00:00       91
    ##  2 1503960366 2016-05-11 22:00:00       47
    ##  3 1503960366 2016-05-11 23:00:00       47
    ##  4 1503960366 2016-05-12 00:00:00       47
    ##  5 1503960366 2016-05-12 01:00:00       47
    ##  6 1503960366 2016-05-12 02:00:00       47
    ##  7 1503960366 2016-05-12 03:00:00       47
    ##  8 1503960366 2016-05-12 04:00:00       47
    ##  9 1503960366 2016-05-12 05:00:00       47
    ## 10 1503960366 2016-05-12 06:00:00       47
    ## # ... with 328 more rows

Not clear what is going on. A clue might be in the days that do not have
the full 24 hours. That is, the hourly data only has 24 hour days. But
the minute data includes the hours from the days where this is not the
case. Even though the number of different rows are small, the
discrepancy should be taken into account depending on the analysis.

#### Intensities and Steps data

We loaded Intensities and steps data, which are also in Wide/Narrow
formats and did the same operations as above for the calories,
therefore, we do not show that code here. Nevertheless, we will merge
the three files into a single minute dataframe called “minuteActivity”
that will be used in the next section.

### Loading METs data

METs, or [Metabolically equivalent
minutes](https://en.wikipedia.org/wiki/Metabolic_equivalent_of_task) is,
according to wikipedia: “the objective measure of the ratio of the rate
at which a person expends energy, relative to the mass of that person”.
It also says it is a way to grade activity levels. Therefore, we will
join it with the minute data set in order to compare the relationships
between them.

``` r
minuteMETsNarrow <- read_csv(paste0(data_path,"minuteMETsNarrow_merged.csv"), 
    col_types = cols(Id = col_character(), 
        ActivityMinute = col_datetime(format = "%m/%d/%Y %H:%M:%S %p")))

mergeMETsActivity <- inner_join(minuteActivity, minuteMETsNarrow)
library(gridExtra)
g1 <- mergeMETsActivity %>% ggplot(aes(x = Intensity, y = METs)) + geom_point() + theme_bw()
g2 <- mergeMETsActivity %>% ggplot(aes(x = Steps, y = METs)) + geom_point() + theme_bw()
g3 <- mergeMETsActivity %>% ggplot(aes(x = Calories, y = METs)) + geom_point() + theme_bw()
g4 <- mergeMETsActivity %>% ggplot(aes(x = Steps, y = Calories)) + geom_point() + theme_bw()
grid.arrange(g1,g2,g3,g4, nrow = 2)
```

![](0_Fit_Bit_Tracker_Data_Sorting_and_Filtering_files/figure-gfm/METs%20loading-1.png)<!-- -->

The relationship between these variables depends on the heart rate and
on the body weight, that is why the graphs are so unclear. Nevertheless,
the graph between METs and calories (bottom left) suggests that there is
linear relationship between them that depends only on each user.

### Loading sleep data

Sleep data by day. Will be interesting to ask if the users who input
sleep data also logged more days or use their device more for longer
during waking hours?

To check the credibility of the data, we plot the difference between the
sleep minutes and wake minutes. We call this tiredness but it could be
due to insomnia or some other factor like misclassification by the
device. No value should be negative, unless they are sleeping on their
feet.

``` r
sleepDay <- read_csv(paste0(data_path,"sleepDay_merged.csv"), 
    col_types = cols(Id = col_character(), 
        SleepDay = col_datetime(format = "%m/%d/%Y %H:%M:%S %p")))

mutate(sleepDay, tiredness = TotalTimeInBed - TotalMinutesAsleep) %>% group_by(Id) %>% summarise(tired = mean(tiredness)) %>% ggplot() + geom_col(aes(x=Id, y= tired)) + theme_bw() + theme(axis.text.x = element_text(angle = 45, hjust = 1)) + labs(ylab = "Minutes", title = "Tiredness", subtitle = "Mean minutes awake in bed by user")
```

![](0_Fit_Bit_Tracker_Data_Sorting_and_Filtering_files/figure-gfm/load%20sleep%20data-1.png)<!-- -->

Most users mean difference is half an hour, or less, meaning they do not
take long to fall sleep and get out of bed easily. Users 1844 and 3977
spend almost 3 hours in bed without sleeping. This could mean their data
is not credible or is part of their routine. Checking user 1844 it seems
that there are only 3 sleep records and all three have the exact same
number of total minutes in bed: 961. This probably means the data for
this user is unreliable. User 3977 seems to have a genuine sleep
problem, since there is no day that they do not spend more than 100
minutes awake in bed. Using the lubridate package function wday() I saw
that this happens any day of the week (I thought they could be only
tracking their sleep on weekends). I will not show data for these users,
but will filter it out.

``` r
sleepDay <- sleepDay %>% filter(Id != 3977333714, Id != 1844505072)

sleepDay %>% ggplot(aes(x = Id, y = TotalMinutesAsleep)) + geom_boxplot() + geom_jitter() + theme_bw() + theme(axis.text.x = element_text(angle = 45, hjust = 1)) + labs(ylab = "Minutes", title = "Total Minutes Asleep", subtitle = "Boxplot of total daily minutes asleep by user")
```

![](0_Fit_Bit_Tracker_Data_Sorting_and_Filtering_files/figure-gfm/sleep-1.png)<!-- -->

From the above graph, it is clear that the data for 2026 and 7007 is
also unreliable due to very few data points and less than 100 minutes of
sleep. It is not clear that we should filter all the records that are
lower than 200 without further data exploration. People are able to
sleep for less than 3 hours for a few days. We will filter records from
users with fewer than 3 days of sleep tracking, though:

``` r
sleepDay <- sleepDay %>% group_by(Id) %>% filter(n()>3)
```

### Load weight info

``` r
weightLogInfo <- read_csv(paste0(data_path,"weightLogInfo_merged.csv"),
    col_types = cols(Id = col_character(), 
        Date = col_datetime(format = "%m/%d/%Y %H:%M:%S %p")))

skim_without_charts(weightLogInfo)
```

|                                                  |               |
|:-------------------------------------------------|:--------------|
| Name                                             | weightLogInfo |
| Number of rows                                   | 67            |
| Number of columns                                | 8             |
| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_   |               |
| Column type frequency:                           |               |
| character                                        | 1             |
| logical                                          | 1             |
| numeric                                          | 5             |
| POSIXct                                          | 1             |
| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ |               |
| Group variables                                  | None          |

Data summary

**Variable type: character**

| skim_variable | n_missing | complete_rate | min | max | empty | n_unique | whitespace |
|:--------------|----------:|--------------:|----:|----:|------:|---------:|-----------:|
| Id            |         0 |             1 |  10 |  10 |     0 |        8 |          0 |

**Variable type: logical**

| skim_variable  | n_missing | complete_rate | mean | count            |
|:---------------|----------:|--------------:|-----:|:-----------------|
| IsManualReport |         0 |             1 | 0.61 | TRU: 41, FAL: 26 |

**Variable type: numeric**

| skim_variable | n_missing | complete_rate |         mean |            sd |           p0 |          p25 |          p50 |          p75 |         p100 |
|:--------------|----------:|--------------:|-------------:|--------------:|-------------:|-------------:|-------------:|-------------:|-------------:|
| WeightKg      |         0 |          1.00 | 7.204000e+01 |         13.92 | 5.260000e+01 | 6.140000e+01 | 6.250000e+01 | 8.505000e+01 | 1.335000e+02 |
| WeightPounds  |         0 |          1.00 | 1.588100e+02 |         30.70 | 1.159600e+02 | 1.353600e+02 | 1.377900e+02 | 1.875000e+02 | 2.943200e+02 |
| Fat           |        65 |          0.03 | 2.350000e+01 |          2.12 | 2.200000e+01 | 2.275000e+01 | 2.350000e+01 | 2.425000e+01 | 2.500000e+01 |
| BMI           |         0 |          1.00 | 2.519000e+01 |          3.07 | 2.145000e+01 | 2.396000e+01 | 2.439000e+01 | 2.556000e+01 | 4.754000e+01 |
| LogId         |         0 |          1.00 | 1.461772e+12 | 782994783\.61 | 1.460444e+12 | 1.461079e+12 | 1.461802e+12 | 1.462375e+12 | 1.463098e+12 |

**Variable type: POSIXct**

| skim_variable | n_missing | complete_rate | min                 | max                 | median              | n_unique |
|:--------------|----------:|--------------:|:--------------------|:--------------------|:--------------------|---------:|
| Date          |         0 |             1 | 2016-04-12 06:47:11 | 2016-05-12 23:59:59 | 2016-04-27 23:59:59 |       56 |

We load the final file. Only 8 users out of the 33 input their weight at
all. Of them, only two do so consistently. It could be interesting to
see if these two represent a distinct segment of users that tracks with
their device more often and are more seriously into fitness? Curiously,
whether the weight is automatically input does not make a difference on
the number of records, suggesting that this is not such an important
feature:

``` r
weightLogInfo %>% group_by(Id, IsManualReport) %>% summarise(count = n(), mean = mean(WeightKg), sd = sd(WeightPounds)) %>% arrange(desc(count))
```

    ## `summarise()` has grouped output by 'Id'. You can override using the `.groups`
    ## argument.

    ## # A tibble: 8 x 5
    ## # Groups:   Id [8]
    ##   Id         IsManualReport count  mean     sd
    ##   <chr>      <lgl>          <int> <dbl>  <dbl>
    ## 1 6962181067 TRUE              30  61.6  0.856
    ## 2 8877689391 FALSE             24  85.1  1.00 
    ## 3 4558609924 TRUE               5  69.6  1.10 
    ## 4 1503960366 TRUE               2  52.6  0    
    ## 5 2873212765 TRUE               2  57    0.935
    ## 6 4319703577 TRUE               2  72.4  0.156
    ## 7 1927972279 FALSE              1 134.  NA    
    ## 8 5577150313 FALSE              1  90.7 NA

Great! We now have a very good idea of how the data is organized and
which kind of questions we can answer with it.

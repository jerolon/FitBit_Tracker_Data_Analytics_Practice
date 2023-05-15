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
or meadian are irrelevant whereas string comparisons like do all have
the same amount of characters and how many are unique are important.

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
- Data was collected for 31 days.
- The minimum for **all** numeric variables is 0. This suggests empty
  days, maybe just days that people did not wear their devices. These
  rows should be eliminated.
- Column names do not need to be cleaned
- The data is in long format
- This data can help answer our question by telling us how frequently
  people track different activities. Before any analysis we can see that
  most activity is done via trackerDistance rather than LoggedActivities

Total distance is not the sum of TrackerDistance and
LoggedActivitiesDistance, as one could assume. Rather it seems to be the
sum of the ActiveDistances columns. Lack of metadata is killing me here.

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

Running `select(dailyActivity, Id, ActivityDate) %>% distinct()` shows
that there are no duplicate dates for any Id

Data seems very redundant. The files `dailyCalories_merged.csv`,
`daily_Intensities_merged` and `dailySteps_merged.csv` are just column
subsets of `dailyActivity_merged.csv`

### Heartrate seconds data

``` r
heartrate_seconds <- read_csv(paste0(data_path,"heartrate_seconds_merged.csv"), 
    col_types = cols(Id = col_character(), Time = col_character(), Value = col_double()))
heartrate_date_time <- transmute(heartrate_seconds, Id, date_time = mdy_hms(Time), Value, hour_of_day = hms::as_hms(date_time), dia = date(date_time))
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
hourlyActivity %>% select(Id, ActivityHour) %>% mutate(fecha = date(ActivityHour)) %>% group_by(Id) %>% mutate(gap = ActivityHour - lag(ActivityHour)) %>% ungroup() %>%  group_by(gap) %>% summarise(gaps = n())
```

    ## # A tibble: 2 x 2
    ##   gap       gaps
    ##   <drtn>   <int>
    ## 1  1 hours 22066
    ## 2 NA hours    33

### Loading minute data

Minute data has the peculiarity that each feature is present in **Wide**
and **Narrow** modalities. My first guess was *Wide* meant that each
user had its oen column, but it is actually that files have a row per
hour and each column is a minute. This is awful, but a good opportunity
to try the package called “lubridate”.

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
#Group the minuteCalories by hour and sum the cals. A column in the summerise function lets us check that we are not including hours where not all minutes are present
minuteCalories2hour <- minuteCalories %>% transmute(Id, 
                                                    ActivityHour = date(ActivityMinute) + hours(hour(ActivityMinute)), 
                                                    Calories) %>% group_by(Id, ActivityHour) %>% summarise(Calories = round(sum(Calories)), n_minutes = n())

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
the full 24 hours.

#### Intensities and Steps data

We loaded Intensities and steps data, which are also in Wide/Narrow
formats and did the same operations as above for the calories,
therefore, we do not show that code here. Nevertheless, we will merge
the three files into a single minute dataframe.

### Loading METs data

METs, or [Metabolically equivalent
minutes](https://en.wikipedia.org/wiki/Metabolic_equivalent_of_task) is,
according to wikipedia: “the objective measure of the ratio of the rate
at which a person expends energy, relative to the mass of that person”.
It also says it is a way to grade activity levels.Therefore, we will
join it with the minute dataset in order to compare the relationships
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
on the body weight. The graph between METs and calories, suggests that
there is linear relationship between them that depends only on each
user.

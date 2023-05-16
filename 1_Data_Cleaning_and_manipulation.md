Data Cleaning and manipulation
================
Jeronimo Miranda
2023-05-16

## Data Loading

I will load the corresponding files from both folders and merge them
using rbind. I will only show the code for loading and merging for
dailyActivity, because it is repetitive, but will show the summaries and
cleaning for all. In any case, the code is still in the .Rmd file.
Aditionally, will remove the intermediate objects, once merged (the rm()
function).

``` r
data_path_A <- "../Fitabase Data 3.12.16-4.11.16/"
data_path_B <- "../Fitabase Data 4.12.16-5.12.16/"
dailyActivity_A <- read_csv(paste0(data_path_A,"dailyActivity_merged.csv"), 
    col_types = cols(Id = col_character(), 
        ActivityDate = col_date(format = "%m/%d/%Y")))

dailyActivity_B <- read_csv(paste0(data_path_B,"dailyActivity_merged.csv"), 
    col_types = cols(Id = col_character(), 
        ActivityDate = col_date(format = "%m/%d/%Y")))

dailyActivity <- rbind(dailyActivity_A, dailyActivity_B)
rm(dailyActivity_A)
rm(dailyActivity_B)
skim_without_charts(dailyActivity)
```

|                                                  |               |
|:-------------------------------------------------|:--------------|
| Name                                             | dailyActivity |
| Number of rows                                   | 1397          |
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
| Id            |         0 |             1 |  10 |  10 |     0 |       35 |          0 |

**Variable type: Date**

| skim_variable | n_missing | complete_rate | min        | max        | median     | n_unique |
|:--------------|----------:|--------------:|:-----------|:-----------|:-----------|---------:|
| ActivityDate  |         0 |             1 | 2016-03-12 | 2016-05-12 | 2016-04-19 |       62 |

**Variable type: numeric**

| skim_variable            | n_missing | complete_rate |    mean |      sd |  p0 |     p25 |     p50 |      p75 |     p100 |
|:-------------------------|----------:|--------------:|--------:|--------:|----:|--------:|--------:|---------:|---------:|
| TotalSteps               |         0 |             1 | 7280.90 | 5214.34 |   0 | 3146.00 | 6999.00 | 10544.00 | 36019.00 |
| TotalDistance            |         0 |             1 |    5.22 |    3.99 |   0 |    2.17 |    4.95 |     7.50 |    28.03 |
| TrackerDistance          |         0 |             1 |    5.19 |    3.98 |   0 |    2.16 |    4.95 |     7.48 |    28.03 |
| LoggedActivitiesDistance |         0 |             1 |    0.13 |    0.70 |   0 |    0.00 |    0.00 |     0.00 |     6.73 |
| VeryActiveDistance       |         0 |             1 |    1.40 |    2.61 |   0 |    0.00 |    0.10 |     1.83 |    21.92 |
| ModeratelyActiveDistance |         0 |             1 |    0.54 |    0.87 |   0 |    0.00 |    0.20 |     0.77 |     6.48 |
| LightActiveDistance      |         0 |             1 |    3.19 |    2.12 |   0 |    1.61 |    3.24 |     4.69 |    12.51 |
| SedentaryActiveDistance  |         0 |             1 |    0.00 |    0.01 |   0 |    0.00 |    0.00 |     0.00 |     0.11 |
| VeryActiveMinutes        |         0 |             1 |   19.68 |   31.68 |   0 |    0.00 |    2.00 |    30.00 |   210.00 |
| FairlyActiveMinutes      |         0 |             1 |   13.40 |   26.40 |   0 |    0.00 |    6.00 |    18.00 |   660.00 |
| LightlyActiveMinutes     |         0 |             1 |  185.37 |  114.06 |   0 |  111.00 |  195.00 |   262.00 |   720.00 |
| SedentaryMinutes         |         0 |             1 |  992.54 |  313.30 |   0 |  729.00 | 1057.00 |  1244.00 |  1440.00 |
| Calories                 |         0 |             1 | 2266.27 |  753.01 |   0 | 1799.00 | 2114.00 |  2770.00 |  4900.00 |

### Daily sleep

Interestingly, there is no daily sleep data for the 3.11 - 4.11 period.

``` r
sleepDay <- read_csv(paste0(data_path_B,"sleepDay_merged.csv"), 
    col_types = cols(Id = col_character(), 
        SleepDay = col_datetime(format = "%m/%d/%Y %H:%M:%S %p")))
skim_without_charts(sleepDay)
```

    ## Warning in kable_pipe(x = structure(c("Name", "Number of rows", "Number of
    ## columns", : The table should have a header (column names)

|                                                  |          |
|:-------------------------------------------------|:---------|
| Name                                             | sleepDay |
| Number of rows                                   | 413      |
| Number of columns                                | 5        |
| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_   |          |
| Column type frequency:                           |          |
| character                                        | 1        |
| numeric                                          | 3        |
| POSIXct                                          | 1        |
| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ |          |
| Group variables                                  | None     |

Data summary

**Variable type: character**

| skim_variable | n_missing | complete_rate | min | max | empty | n_unique | whitespace |
|:--------------|----------:|--------------:|----:|----:|------:|---------:|-----------:|
| Id            |         0 |             1 |  10 |  10 |     0 |       24 |          0 |

**Variable type: numeric**

| skim_variable      | n_missing | complete_rate |   mean |     sd |  p0 | p25 | p50 | p75 | p100 |
|:-------------------|----------:|--------------:|-------:|-------:|----:|----:|----:|----:|-----:|
| TotalSleepRecords  |         0 |             1 |   1.12 |   0.35 |   1 |   1 |   1 |   1 |    3 |
| TotalMinutesAsleep |         0 |             1 | 419.47 | 118.34 |  58 | 361 | 433 | 490 |  796 |
| TotalTimeInBed     |         0 |             1 | 458.64 | 127.10 |  61 | 403 | 463 | 526 |  961 |

**Variable type: POSIXct**

| skim_variable | n_missing | complete_rate | min        | max        | median     | n_unique |
|:--------------|----------:|--------------:|:-----------|:-----------|:-----------|---------:|
| SleepDay      |         0 |             1 | 2016-04-12 | 2016-05-12 | 2016-04-27 |       31 |

### Weight info

Weight info is absent in the 3.11 - 4.11 period too

``` r
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

### Hourly data

``` r
skim_without_charts(hourlyActivity)
```

|                                                  |                |
|:-------------------------------------------------|:---------------|
| Name                                             | hourlyActivity |
| Number of rows                                   | 47233          |
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
| Id            |         0 |             1 |  10 |  10 |     0 |       35 |          0 |

**Variable type: numeric**

| skim_variable    | n_missing | complete_rate |   mean |     sd |  p0 | p25 |   p50 |    p75 |  p100 |
|:-----------------|----------:|--------------:|-------:|-------:|----:|----:|------:|-------:|------:|
| Calories         |         0 |             1 |  95.43 |  60.06 |  42 |  62 | 80.00 | 105.00 |   948 |
| TotalIntensity   |         0 |             1 |  11.30 |  20.73 |   0 |   0 |  2.00 |  15.00 |   180 |
| AverageIntensity |         0 |             1 |   0.19 |   0.35 |   0 |   0 |  0.03 |   0.25 |     3 |
| StepTotal        |         0 |             1 | 300.08 | 678.20 |   0 |   0 | 17.00 | 317.00 | 10565 |

**Variable type: POSIXct**

| skim_variable | n_missing | complete_rate | min        | max                 | median              | n_unique |
|:--------------|----------:|--------------:|:-----------|:--------------------|:--------------------|---------:|
| ActivityHour  |         0 |             1 | 2016-03-12 | 2016-05-12 15:00:00 | 2016-04-11 11:00:00 |     1480 |

### Minute data

I will only check the narrow minute files.

``` r
minuteActivity <- inner_join(minuteCalories, minuteIntensities, by = c("Id","ActivityMinute")) %>% inner_join(minuteSteps, by = c("Id", "ActivityMinute"))
```

    ## Warning in inner_join(minuteCalories, minuteIntensities, by = c("Id",
    ## "ActivityMinute")): Detected an unexpected many-to-many relationship between `x`
    ## and `y`.

    ## Warning in inner_join(., minuteSteps, by = c("Id", "ActivityMinute")): Detected
    ## an unexpected many-to-many relationship between `x` and `y`.

``` r
skim_without_charts(minuteActivity)
```

    ## Warning in kable_pipe(x = structure(c("Name", "Number of rows", "Number of
    ## columns", : The table should have a header (column names)

|                                                  |                |
|:-------------------------------------------------|:---------------|
| Name                                             | minuteActivity |
| Number of rows                                   | 2833620        |
| Number of columns                                | 5              |
| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_   |                |
| Column type frequency:                           |                |
| character                                        | 1              |
| numeric                                          | 3              |
| POSIXct                                          | 1              |
| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ |                |
| Group variables                                  | None           |

Data summary

**Variable type: character**

| skim_variable | n_missing | complete_rate | min | max | empty | n_unique | whitespace |
|:--------------|----------:|--------------:|----:|----:|------:|---------:|-----------:|
| Id            |         0 |             1 |  10 |  10 |     0 |       35 |          0 |

**Variable type: numeric**

| skim_variable | n_missing | complete_rate | mean |    sd |  p0 |  p25 |  p50 |  p75 |   p100 |
|:--------------|----------:|--------------:|-----:|------:|----:|-----:|-----:|-----:|-------:|
| Calories      |         0 |             1 | 1.59 |  1.38 |   0 | 0.94 | 1.22 | 1.43 |  23.01 |
| Intensity     |         0 |             1 | 0.19 |  0.51 |   0 | 0.00 | 0.00 | 0.00 |   3.00 |
| Steps         |         0 |             1 | 5.00 | 17.62 |   0 | 0.00 | 0.00 | 0.00 | 220.00 |

**Variable type: POSIXct**

| skim_variable  | n_missing | complete_rate | min        | max                 | median              | n_unique |
|:---------------|----------:|--------------:|:-----------|:--------------------|:--------------------|---------:|
| ActivityMinute |         0 |             1 | 2016-03-12 | 2016-05-12 15:59:00 | 2016-04-11 11:16:00 |    88800 |

Note that the `echo = FALSE` parameter was added to the code chunk to
prevent printing of the R code that generated the plot.

## Plotting a few explorations

What’s the relationship between steps taken in a day and sedentary
minutes? How could this help inform the customer segments that we can
market to? E.g. position this more as a way to get started in walking
more? Or to measure steps that you’re already taking?

``` r
ggplot(data=dailyActivity, aes(x=TotalSteps, y=SedentaryMinutes)) + geom_point()
```

![](1_Data_Cleaning_and_manipulation_files/figure-gfm/unnamed-chunk-2-1.png)<!-- -->

What’s the relationship between minutes asleep and time in bed? You
might expect it to be almost completely linear - are there any
unexpected trends?

``` r
ggplot(data=sleepDay, aes(x=TotalMinutesAsleep, y=TotalTimeInBed)) + geom_point()
```

![](1_Data_Cleaning_and_manipulation_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

What could these trends tell you about how to help market this product?
Or areas where you might want to explore further?

## Merging these two datasets together

Take a look at how many participants are in this data set.

``` r
#n_distinct(combined_data$Id)
```

Note that there were more participant Ids in the daily activity dataset
that have been filtered out using merge. Consider using ‘outer_join’ to
keep those in the dataset.

Now you can explore some different relationships between activity and
sleep as well. For example, do you think participants who sleep more
also take more steps or fewer steps per day? Is there a relationship at
all? How could these answers help inform the marketing strategy of how
you position this new product?

This is just one example of how to get started with this data - there
are many other files and questions to explore as well!

``` r
skim_without_charts(heartrate_seconds)
```

|                                                  |                   |
|:-------------------------------------------------|:------------------|
| Name                                             | heartrate_seconds |
| Number of rows                                   | 3638339           |
| Number of columns                                | 3                 |
| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_   |                   |
| Column type frequency:                           |                   |
| character                                        | 1                 |
| numeric                                          | 1                 |
| POSIXct                                          | 1                 |
| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ |                   |
| Group variables                                  | None              |

Data summary

**Variable type: character**

| skim_variable | n_missing | complete_rate | min | max | empty | n_unique | whitespace |
|:--------------|----------:|--------------:|----:|----:|------:|---------:|-----------:|
| Id            |         0 |             1 |  10 |  10 |     0 |       15 |          0 |

**Variable type: numeric**

| skim_variable | n_missing | complete_rate | mean |    sd |  p0 | p25 | p50 | p75 | p100 |
|:--------------|----------:|--------------:|-----:|------:|----:|----:|----:|----:|-----:|
| Value         |         0 |             1 | 78.1 | 19.23 |  36 |  64 |  74 |  88 |  203 |

**Variable type: POSIXct**

| skim_variable | n_missing | complete_rate | min                 | max                 | median              | n_unique |
|:--------------|----------:|--------------:|:--------------------|:--------------------|:--------------------|---------:|
| Time          |         0 |             1 | 2016-03-29 00:00:05 | 2016-05-12 16:20:00 | 2016-04-19 17:30:00 |  1456216 |

## Check Ids for all files

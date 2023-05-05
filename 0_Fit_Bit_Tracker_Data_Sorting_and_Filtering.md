0_Fit_Bit_Tracker_Data_Sorting_and_Filtering
================
Jeronimo Miranda
2023-05-05

## Loading and exploring the data

We are using the data from
<https://www.kaggle.com/datasets/arashnic/fitbit>. Assume the path to
the data is in a folder above where the repository is. \### Daily
activity data

``` r
data_path <- "../Fitabase Data 4.12.16-5.12.16/"
dailyActivity <- read_csv(paste0(data_path,"dailyActivity_merged.csv"), 
    col_types = cols(Id = col_character(), 
        ActivityDate = col_date(format = "%m/%d/%Y")))
skim_without_charts(dailyActivity)
```

    ## Warning in kable_pipe(x = structure(c("Name", "Number of rows", "Number of
    ## columns", : The table should have a header (column names)

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

- Data was collected for 31 days.
- 33 unique Ids, all have 10 characters.
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

### Number of days tracked

We group the days by Id and then plot a histogram of how many days
people in the dataset were tracked

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

![](0_Fit_Bit_Tracker_Data_Sorting_and_Filtering_files/figure-gfm/number%20of%20days%20tracked-1.png)<!-- -->

Running `select(dailyActivity, Id, ActivityDate) %>% distinct()` shows
that there are no duplicate dates for any Id

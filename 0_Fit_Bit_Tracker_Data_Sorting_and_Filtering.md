0_Fit_Bit_Tracker_Data_Sorting_and_Filtering
================
Jeronimo Miranda
2023-05-05

## Loading the data

We are using the data from
<https://www.kaggle.com/datasets/arashnic/fitbit>. Assume the path to
the data is in a folder above where the repository is.

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

Already loaded the dates in the correct format. Since apart from date
and Id all columns are numeric, it makes sense to use
skim_without_charts which gives a statistical summary of each variable.
The function output is rendered as an html table, which is quite nice.

## Including Plots

You can also embed plots, for example:

Note that the `echo = FALSE` parameter was added to the code chunk to
prevent printing of the R code that generated the plot.

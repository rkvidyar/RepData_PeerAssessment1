---
title: "Reproducible Research: Peer Assessment 1"
author: "Raman"
date: "3/9/2022"
output: 
  html_document: 
    keep_md: yes
---



## R Markdown

This is an R Markdown document. Markdown is a simple formatting syntax for authoring HTML, PDF, and MS Word documents. For more details on using R Markdown see <http://rmarkdown.rstudio.com>.

When you click the **Knit** button a document will be generated that includes both content as well as the output of any embedded R code chunks within the document. You can embed an R code chunk like this:

Note that the `echo = TRUE` parameter was added to the code chunk to print the R code that generated the plot. Also, assumes dplyr and reshape2 packages are already installed. 


## Background Info

This assignment makes use of data from a personal activity monitoring device. This device collects
data at 5 minute intervals through out the day. The data consists of two months of data from an
anonymous individual collected during the months of October and November, 2012 and include the
number of steps taken in 5 minute intervals each day.

The data (Dataset: Activity monitoring data) for this assignment can be downloaded from the course web site: 
https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip. 

Here we load the data and give the data set the name activity. Below is the code for reading in the dataset and/or processing the data.  



```r
td <- tempdir()  ## create a temporary directory
tf <- tempfile(tmpdir=td, fileext=".zip")  ## create the placeholder file
url <- "https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip"  ## web address of zip file
download.file(url, method = "curl", tf) ## download into the placeholder file
activity <- read.csv(unzip(tf))  ## unzip the downloaded file and read it into an R data set
```

## More background info:

The variables included in this dataset are:

- steps: Number of steps taking in a 5-minute interval (missing values are coded as NA)

- date: The date on which the measurement was taken in YYYY-MM-DD format

- interval: Identifier for the 5-minute interval in which measurement was taken

The dataset is stored in a comma-separated-value (CSV) file and there are a total of 17,568
observations in this dataset.

Here we view the names of the data set and confirm the variables are steps, date and interval.  


```r
names(activity)
```

```
## [1] "steps"    "date"     "interval"
```

Next, we review the first 6 lines of the data set. Confirm the variables as before and see there are NA values for the steps variable. 

```r
head(activity)
```

```
##   steps       date interval
## 1    NA 2012-10-01        0
## 2    NA 2012-10-01        5
## 3    NA 2012-10-01       10
## 4    NA 2012-10-01       15
## 5    NA 2012-10-01       20
## 6    NA 2012-10-01       25
```
Next we review the number of rows and columns of the data set. Confirm 17,568 rows or observations of data.  

```r
dim(activity)
```

```
## [1] 17568     3
```
Next use the summary command to view the number of NA values in the activity data set and learn about the characteristics of the variables. Here we see 2304 NA values in the steps variable:

```r
summary(activity)
```

```
##      steps            date              interval     
##  Min.   :  0.00   Length:17568       Min.   :   0.0  
##  1st Qu.:  0.00   Class :character   1st Qu.: 588.8  
##  Median :  0.00   Mode  :character   Median :1177.5  
##  Mean   : 37.38                      Mean   :1177.5  
##  3rd Qu.: 12.00                      3rd Qu.:1766.2  
##  Max.   :806.00                      Max.   :2355.0  
##  NA's   :2304
```
Confirmed number of NA values in the steps variable is 2304:

```r
sum(is.na(activity$steps))
```

```
## [1] 2304
```
Next confirm the activity data set is a data frame and has 17568 observations of 3 variables. 

```r
str(activity)
```

```
## 'data.frame':	17568 obs. of  3 variables:
##  $ steps   : int  NA NA NA NA NA NA NA NA NA NA ...
##  $ date    : chr  "2012-10-01" "2012-10-01" "2012-10-01" "2012-10-01" ...
##  $ interval: int  0 5 10 15 20 25 30 35 40 45 ...
```
Next find the number of zero values for the steps variable

```r
head(table(activity$steps))
```

```
## 
##     0     1     2     3     4     5 
## 11014     7     8     3    25    17
```
Next find the number of unique dates in the activity data set

```r
unique(activity$date)  
```

```
##  [1] "2012-10-01" "2012-10-02" "2012-10-03" "2012-10-04" "2012-10-05"
##  [6] "2012-10-06" "2012-10-07" "2012-10-08" "2012-10-09" "2012-10-10"
## [11] "2012-10-11" "2012-10-12" "2012-10-13" "2012-10-14" "2012-10-15"
## [16] "2012-10-16" "2012-10-17" "2012-10-18" "2012-10-19" "2012-10-20"
## [21] "2012-10-21" "2012-10-22" "2012-10-23" "2012-10-24" "2012-10-25"
## [26] "2012-10-26" "2012-10-27" "2012-10-28" "2012-10-29" "2012-10-30"
## [31] "2012-10-31" "2012-11-01" "2012-11-02" "2012-11-03" "2012-11-04"
## [36] "2012-11-05" "2012-11-06" "2012-11-07" "2012-11-08" "2012-11-09"
## [41] "2012-11-10" "2012-11-11" "2012-11-12" "2012-11-13" "2012-11-14"
## [46] "2012-11-15" "2012-11-16" "2012-11-17" "2012-11-18" "2012-11-19"
## [51] "2012-11-20" "2012-11-21" "2012-11-22" "2012-11-23" "2012-11-24"
## [56] "2012-11-25" "2012-11-26" "2012-11-27" "2012-11-28" "2012-11-29"
## [61] "2012-11-30"
```
Load the dplyr package and convert activity data frame into tbl data frame

```r
library(dplyr)
```

```
## 
## Attaching package: 'dplyr'
```

```
## The following objects are masked from 'package:stats':
## 
##     filter, lag
```

```
## The following objects are masked from 'package:base':
## 
##     intersect, setdiff, setequal, union
```

```r
activity <- tbl_df(activity)
```

```
## Warning: `tbl_df()` was deprecated in dplyr 1.0.0.
## Please use `tibble::as_tibble()` instead.
```
Here, we filter out the rows with missing values in the steps column of the activity data frame. We find 15264 rows of non-NA or non-missing values in activity. 

```r
activity_noNA <- filter(activity, !is.na(steps))
str(activity_noNA)  
```

```
## tibble[,3] [15,264 x 3] (S3: tbl_df/tbl/data.frame)
##  $ steps   : int [1:15264] 0 0 0 0 0 0 0 0 0 0 ...
##  $ date    : chr [1:15264] "2012-10-02" "2012-10-02" "2012-10-02" "2012-10-02" ...
##  $ interval: int [1:15264] 0 5 10 15 20 25 30 35 40 45 ...
```
Test to confirm that there are missing values in the activity data set. The response here is TRUE, so there are NO missing values.

```r
all(colSums(is.na(activity_noNA))==0)  
```

```
## [1] TRUE
```
Next, get a table of the total number of steps by date of the activity data frame with no missing values.

```r
daily_steps <- tapply(activity_noNA$steps, activity_noNA$date, sum)
```
Histogram of the total number of steps taken each day with rug

```r
hist(daily_steps, breaks=15, main = paste("Histogram of the total number of steps taken each day"), xlab="daily steps")
rug(daily_steps)
```

![](PA1_template_files/figure-html/Step_Histogram-1.png)<!-- -->

Calculate and report the mean and median of the total number of steps taken per day: Mean of 10766 and a Median of 10765.

```r
mean(daily_steps)   
```

```
## [1] 10766.19
```

```r
median(daily_steps)  
```

```
## [1] 10765
```
Calculate and Time Series Plot mean number of steps by interval number across the days.

```r
mean_int_steps <- tapply(activity_noNA$steps, activity_noNA$interval, mean) 
plot(unique(activity_noNA$interval), mean_int_steps, type="l", xlab="5-minute interval", ylab="average number of steps taken")
```

![](PA1_template_files/figure-html/Calc_Mean_Steps_by_Interval-1.png)<!-- -->

The 5-minute interval, on average across all the days in the activity data set that contains the maximum number of steps

```r
mean_int_steps[which.max(mean_int_steps)]
```

```
##      835 
## 206.1698
```
Here we begin code to describe and show a strategy for imputing missing data. 
Calculate and report the total number of missing values in the dataset. Here, we see only the steps variable has missing or NA values and there are 2304 NA's. 

```r
summary(activity)
```

```
##      steps            date              interval     
##  Min.   :  0.00   Length:17568       Min.   :   0.0  
##  1st Qu.:  0.00   Class :character   1st Qu.: 588.8  
##  Median :  0.00   Mode  :character   Median :1177.5  
##  Mean   : 37.38                      Mean   :1177.5  
##  3rd Qu.: 12.00                      3rd Qu.:1766.2  
##  Max.   :806.00                      Max.   :2355.0  
##  NA's   :2304
```
Calculate a table of the mean number of steps by date of the activity data frame excluding missing or NA values in the calculation.

```r
daily_mean_steps <- tapply(activity$steps, activity$date, mean, na.rm=TRUE)
```
Determine the number of NA values if any in daily_mean_steps. Here we see there are 8 NA or missing values, since we asked it to take the mean of missing or NA values. 

```r
summary(daily_mean_steps)
```

```
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max.    NA's 
##  0.1424 30.6979 37.3785 37.3826 46.1597 73.5903       8
```
Reshape daily_mean_steps so that we can begin process of cleaning up missing or NA values from daily_mean_steps; daily_mean_steps is reorganized into 2 columns of date and daily_mean_steps variables.

```r
library(reshape2)
daily_mean_steps_melt <- melt(daily_mean_steps)
names(daily_mean_steps_melt) <- c("date", "daily_mean_steps")
```
Rename daily_mean_steps_melt into dm. Also, review dm data set. Changed date in dm data set to a date type variable. 

```r
dm <- daily_mean_steps_melt
str(dm)
```

```
## 'data.frame':	61 obs. of  2 variables:
##  $ date            : Factor w/ 61 levels "2012-10-01","2012-10-02",..: 1 2 3 4 5 6 7 8 9 10 ...
##  $ daily_mean_steps: num  NaN 0.438 39.417 42.069 46.16 ...
```

```r
dm$date <- as.Date(dm$date)
```
Change the date variable in the activity data set to a date type variable

```r
activity$date <- as.Date(activity$date)
```
Calculation of mean value of daily steps across all days. The value is 37.3826

```r
mean(dm$daily_mean_steps, na.rm=TRUE)
```

```
## [1] 37.3826
```
Here the daily_mean_steps variable of dm is cleaned up of all NA or missing values, so what was previously an NA value will be the average of all daily means steps (or 37.3826). This is more practical because if you fill in the NA value with just the mean for the given date then you will get a lot of zero values which is not very informative. Follow up with summary() command to confirm there are no NA values.  

```r
for(z in 1:nrow(dm))
  {if(is.na(dm$daily_mean_steps[z]))
    {dm$daily_mean_steps[z] <- mean(dm$daily_mean_steps, na.rm=TRUE)}}
summary(dm)
```

```
##       date            daily_mean_steps 
##  Min.   :2012-10-01   Min.   : 0.1424  
##  1st Qu.:2012-10-16   1st Qu.:34.0938  
##  Median :2012-10-31   Median :37.3826  
##  Mean   :2012-10-31   Mean   :37.3826  
##  3rd Qu.:2012-11-15   3rd Qu.:44.4826  
##  Max.   :2012-11-30   Max.   :73.5903
```
Here we have a nested for loop, that goes down the list for the steps variable of the activity data set. It tests each value of steps for an NA or missing value. If it finds an NA, it then matches by date from the dm table (daily_mean_steps_melt) and replaces the NA value with the mean steps value for that date. 

```r
for(q in 1:nrow(activity)) 
  {if(is.na(activity$steps[q])) 
    {for(p in 1:nrow(dm))
      {if(activity$date[q]==dm$date[p]) 
        {activity$steps[q] <- dm$daily_mean_steps[p]}}}}
```
Now that we have filled in all NA or missing values in the activity data set, we can now calculate a table of the total number of steps by date.

```r
daily_steps2 <- tapply(activity$steps, activity$date, sum)
```
Histogram of the total number of steps taken each day with rug after missing values are imputed - version 2

```r
hist(daily_steps2, breaks=15, main = paste("Histogram of the total number of steps taken each day"), xlab="daily steps")
rug(daily_steps2)
```

![](PA1_template_files/figure-html/Step_Histogram2-1.png)<!-- -->

Calculate and report the mean and median of the total number of steps taken per day - version 2: Mean of 10766 and a Median of 10766. After adding imputed values the mean did not change. The median increased a minimal amount. 

```r
mean(daily_steps2)
```

```
## [1] 10766.19
```

```r
median(daily_steps2)
```

```
## [1] 10766.19
```
Add and fill in the type of day variable to the activity data set

```r
for(c in 1:nrow(activity)) 
  {if(weekdays(strptime(activity$date[c], "%Y-%m-%d"))=="Sunday" | weekdays(strptime(activity$date[c], "%Y-%m-%d"))=="Saturday") 
    {activity$type_of_day[c] <- "weekend"} 
      else {activity$type_of_day[c] <- "weekday"}}
```

```
## Warning: Unknown or uninitialised column: `type_of_day`.
```
Change the type of day variable to a factor variable in the activity data set

```r
activity$type_of_day <- as.factor(activity$type_of_day)
```
Count the number of weekday and weekend days - 12960 days on weekday and 4608 days on the weekend

```r
count(activity, type_of_day)
```

```
## # A tibble: 2 x 2
##   type_of_day     n
##   <fct>       <int>
## 1 weekday     12960
## 2 weekend      4608
```
Create a table that calculates average steps by interval and type of day.

```r
average_steps <- tapply(activity$steps, list(activity$interval, activity$type_of_day), mean) 
```
Reshape the data in average_steps into columns of interval, type_of_day and average_steps

```r
stepMelt <- melt(average_steps, id=c("weekday", "weekend"))
names(stepMelt) <- c("interval", "type_of_day", "average_steps")
```
Create a panel plot containing a time series plot of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). Panel plot comparing the average number of steps taken per 5-minute interval across weekdays and weekends. 

```r
library(lattice)
xyplot(average_steps ~ interval | type_of_day, data = stepMelt, type="l", layout = c(1, 2), ylab="Number of steps", xlab="Interval")
```

![](PA1_template_files/figure-html/panel_time_series_plot-1.png)<!-- -->


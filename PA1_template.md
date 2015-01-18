# Reproducible Research: Peer Assessment 1


## Loading and preprocessing the data
Start by loading up the data.
Loading up dplyr.

```r
library(dplyr)
library(ggplot2)
library(scales)
data <- read.csv("./activity.csv", stringsAsFactors = FALSE)
data <- mutate(data, date = as.Date(data$date, "%Y-%m-%d"))
```

## What is mean total number of steps taken per day?
Prepare the data by group using 'date'
then calculate sum of steps taken each day

```r
dataGroupByDate <- group_by(data, date)
sumStepByDate <- summarise_each(dataGroupByDate, funs(sum(., na.rm = TRUE)))
```
then start plotting 

```r
sumStepByDate$date <- as.POSIXlt(sumStepByDate$date)
g <- ggplot(sumStepByDate, aes(date, steps))
g <- g + geom_histogram(stat="identity")
g <- g + scale_x_datetime(labels = date_format("%m-%d"),breaks = date_breaks(width="1 week"),minor_breaks = date_breaks(width = "1 day"))
print(g)
```

![](./PA1_template_files/figure-html/unnamed-chunk-2-1.png) 

### Calculate and report the mean and median total number of steps taken per day 

```r
print(mean(sumStepByDate$steps))
```

```
## [1] 9354.23
```

```r
print(median(sumStepByDate$steps))
```

```
## [1] 10395
```
## What is the average daily activity pattern?

```r
dataGroupByInterval <- group_by(data, interval)
dataGroupByInterval <- dataGroupByInterval[,c(1,3)]
aveStepsByInterval <- summarise_each(dataGroupByInterval, funs(mean(., na.rm=TRUE)))
g2 <- ggplot(aveStepsByInterval, aes(interval, steps))
g2 <- g2 + geom_line()
print(g2)
```

![](./PA1_template_files/figure-html/unnamed-chunk-4-1.png) 

### Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?


```r
print(top_n(aveStepsByInterval, n=1))
```

```
## Source: local data frame [1 x 2]
## 
##   interval    steps
## 1      835 206.1698
```
The 835 interval is 8.35 AM 

## Imputing missing values

### Calculate and report the total number of missing values in the dataset.


```r
print(sum(is.na(data$steps)))
```

```
## [1] 2304
```

### Fill the missing data with average number of that interval

```r
## start by copy data to another dataset
filledData <- data
## using aveStepsByInterval dataset to fill NA data with average steps of that time interval
filledData <- cbind(filledData, averagesteps = aveStepsByInterval$steps)
filledData <- mutate(filledData, steps = ifelse(is.na(steps), averagesteps, steps))
```

### Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. 

```r
dataGroupByDate2 <- group_by(filledData, date)
sumStepByDate2 <- summarise_each(dataGroupByDate2, funs(sum(., na.rm = TRUE)))
sumStepByDate2$date <- as.POSIXlt(sumStepByDate2$date)

g3 <- ggplot(sumStepByDate2, aes(date, steps))
g3 <- g3 + geom_histogram(stat="identity")
g3 <- g3 + scale_x_datetime(labels = date_format("%m-%d"),breaks = date_breaks(width="1 week"),minor_breaks = date_breaks(width = "1 day"))
print(g3)
```

![](./PA1_template_files/figure-html/unnamed-chunk-8-1.png) 

```r
print(mean(sumStepByDate2$steps))
```

```
## [1] 10766.19
```

```r
print(median(sumStepByDate2$steps))
```

```
## [1] 10766.19
```

###Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?




## Are there differences in activity patterns between weekdays and weekends?

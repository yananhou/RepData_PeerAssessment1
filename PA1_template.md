``` r
---
title: 'Reproducible Research: Peer Assessment 1'
author: "Yanan Hou"
date: "August 27, 2017"
output: "html_documet"
keep_md: "true"
---
```

# Reproducible Research: Peer Assessment 1

## Introduction
It is now possible to collect a large amount of data about personal movement using activity monitoring devices such as a Fitbit, Nike Fuelband, or Jawbone Up. These types of devices are part of the "quantified self" movement - a group of enthusiasts who take measurements about themselves regularly to improve their health, to find patterns in their behavior, or because they are tech geeks. But these data remain under-utilized both because the raw data are hard to obtain and there is a lack of statistical methods and software for processing and interpreting the data.

This assignment makes use of data from a personal activity monitoring device. This device collects data at 5 minute intervals through out the day. The data consists of two months of data from an anonymous individual collected during the months of October and November, 2012 and include the number of steps taken in 5 minute intervals each day.


## Loading and processing the data

```r
setwd("C:/Users/Hou/Documents/coursera/Reproducible Research")
data <- read.csv("activity.csv")
data$date <- as.Date(data$date)
```


## What is the mean total number of steps taken per day?

```r
# Calculate the total number of steps taken per day
perDay <- aggregate(data$steps, list(data$date), sum)

# Make a histogram of the total number of steps taken each day (rows with missing values are removed)
library(ggplot2)
ggplot(data, aes(x = date, y = steps)) + geom_bar(stat = "identity")
```

```
## Warning: Removed 2304 rows containing missing values (position_stack).
```

![plot of chunk unnamed-chunk-2](figure/unnamed-chunk-2-1.png)


```r
# Mean of the total number of steps taken per day
mean(perDay$x, na.rm = TRUE)
```

```
## [1] 10766.19
```


```r
# Median of the total number of steps taken per day
median(perDay$x, na.rm = TRUE)
```

```
## [1] 10765
```


## What is the average daily activity pattern?

```r
perInterval<-aggregate(data$steps, list(data$interval), mean, na.rm=TRUE)

# Time series plot
ggplot(perInterval, aes(x=Group.1, y=x))+geom_line()
```

![plot of chunk unnamed-chunk-5](figure/unnamed-chunk-5-1.png)


```r
# Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?
perInterval[which(perInterval$x== max(perInterval$x)),1]
```

```
## [1] 835
```


## Imputing missing values

```r
# Calculate the total number of missing values in the dataset
sum(is.na(data$steps))
```

```
## [1] 2304
```


```r
# Create a new dataset with the same dimension as the orignal one
imputeData<-data

# Devise a strategy for filling in all of the missing values in the dataset: missing values are replaced with the mean total number of steps taken per day
imputeData[is.na(imputeData$steps),1]<- as.integer(perInterval$x)

# Histogram of the total number of steps taken each day
ggplot(imputeData, aes(x=date, y=steps))+geom_bar(stat = "identity")
```

![plot of chunk unnamed-chunk-8](figure/unnamed-chunk-8-1.png)


```r
# Calculate the mean and median
perDayImputed<-aggregate(imputeData$steps, list(imputeData$date), sum)
mean(perDay$x, na.rm = TRUE)
```

```
## [1] 10766.19
```


```r
median(perDay$x, na.rm = TRUE)
```

```
## [1] 10765
```
Do these values differ from the estimates from the first part of the assignment? Yes.

What is the impact of imputing missing data on the estimates of the total daily number of steps? It has increased.


## Are there differences in activity patterns between weekdays and weekends?

```r
weekdays1 <- c('Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday')

# Create a new factor variable in the dataset with two levels 
imputeData$wDay <- factor((weekdays(imputeData$date) %in% weekdays1),levels=c(FALSE, TRUE), labels=c('weekend', 'weekday'))
perIntervalImpute<-aggregate(imputeData$steps, list(imputeData$interval, imputeData$wDay), mean)

# Make a panel plot containing a time series plot
ggplot(perIntervalImpute, aes(x=Group.1, y=x, color = Group.2,group=Group.2))+geom_line()+facet_wrap(~Group.2,nrow = 2)
```

![plot of chunk unnamed-chunk-11](figure/unnamed-chunk-11-1.png)

Maximum average number of steps during weekday is higher than weekend. But there tends to be more activities during weekends than on weekdays. People are generally most active in the morning while there is very little activities in the late evening and early morning.
```
```
```
```
```


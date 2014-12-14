---
title: "Reproducible Research: Peer Assessment 1"
output: 
  html_document:
    keep_md: true
---


### Dataset

* We load data describing the number of steps taken as measured by a pedometer worn by a subject over the course of two months
* Assumes current working directoty contains file "activity.zip" containing "activity.csv" featuring measurements as retrievable from [Github][repo]


```r
library(ggplot2)
library(plyr)
library(data.table)
activity <- read.csv(unz("activity.zip", filename = "activity.csv"))
adt <- data.table(activity)
```

### Number of steps taken each day


```r
steps_per_day <- adt[,list(steps=sum(steps)),by=date]
hist(steps_per_day$steps, breaks = "FD")
```

![plot of chunk unnamed-chunk-2](figure/unnamed-chunk-2-1.png) 

```r
mean_steps_per_day <- mean(steps_per_day$steps, na.rm = TRUE)
median_steps_per_day <- median(steps_per_day$steps, na.rm = TRUE)
```
Mean steps per day: 1.0766189 &times; 10<sup>4</sup>  
Median steps per day: 10765  

### Average daily activity pattern

Average number of steps taken per five minute period, averaged over all days


```r
steps_by_time_of_day <- adt[,list(avsteps=mean(steps, na.rm=TRUE)),by=interval]
plot(steps_by_time_of_day$interval, steps_by_time_of_day$avsteps, type = "l")
```

![plot of chunk unnamed-chunk-3](figure/unnamed-chunk-3-1.png) 

```r
time_with_max_steps <- steps_by_time_of_day$interval[which.max(steps_by_time_of_day$avsteps)]
hours <- floor(time_with_max_steps / 60)
minutes = floor(((time_with_max_steps / 60) - hours) * 60)
```

Interval 835, corresponding to 13:54 has on average the most steps.

### Number of steps taken each day (imputed)

Note that there are a number of days/intervals where there are missing values (coded as NA). The presence of missing days may introduce bias into some calculations or summaries of the data.

Total number of rows with missing values: 2304

We have created a second dataset with missing values imputed using the average for the missing time of day.



```r
activityimputed <- merge(activity, data.frame(steps_by_time_of_day))
activityimputed$steps[is.na(activityimputed$steps)] <- activityimputed$avsteps[is.na(activityimputed$steps)]
activityimputed <- activityimputed[, 1:3]
aidt <- data.table(activityimputed)
imputed_steps_per_day <- aidt[,list(steps=sum(steps)),by=date]
hist(imputed_steps_per_day$steps, breaks = "FD")
```

![plot of chunk unnamed-chunk-4](figure/unnamed-chunk-4-1.png) 

```r
mean_steps_per_day <- mean(imputed_steps_per_day$steps, na.rm = TRUE)
median_steps_per_day <- median(imputed_steps_per_day$steps, na.rm = TRUE)
```
Mean steps per day: 1.0766189 &times; 10<sup>4</sup>  
Median steps per day: 1.0766189 &times; 10<sup>4</sup>  

[repo]: https://github.com/solatido/RepData_PeerAssessment1/tree/80edf39c3bb508fee88e3394542f967dd3fd3270

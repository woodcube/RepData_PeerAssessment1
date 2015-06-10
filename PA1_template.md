# Reproducible Research: Peer Assessment 1


## Loading and preprocessing the data
Show any code that is needed to

1. Load the data (i.e. read.csv())

2. Process/transform the data (if necessary) into a format suitable for your analysis


```r
data <- read.csv(unz('activity.zip', 'activity.csv'))
```


## What is mean total number of steps taken per day?
For this part of the assignment, you can ignore the missing values in the dataset.

1. Calculate the total number of steps taken per day

```r
library(dplyr)

steps.day1 <-
    data %>%
    group_by(date) %>%
    summarize(steps=sum(steps))
```

2. If you do not understand the difference between a histogram and a barplot, research the difference between them. Make a histogram of the total number of steps taken each day


```r
hist(steps.day1$steps, xlab='Steps', main='Total Steps per Day')
```

![](PA1_template_files/figure-html/unnamed-chunk-3-1.png) 

3. Calculate and report the mean and median of the total number of steps taken per day

```r
mu1 <- mean(steps.day1$steps, na.rm=TRUE)
med1 <- median(steps.day1$steps, na.rm=TRUE)

options(scipen=100, digits=2)
```
the total number of steps taken per day

* mean: 10766.19.
* median: 10765.

## What is the average daily activity pattern?
1. Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)

```r
steps.interval <-
    data %>%
    group_by(interval) %>%
    summarize(steps=mean(steps, na.rm=TRUE))

with(steps.interval,
     plot(interval, steps, type='l',
          main='Average steps of each 5-min interval')
     )
```

![](PA1_template_files/figure-html/unnamed-chunk-5-1.png) 

2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

```r
interval.maxsteps <-
    steps.interval$interval[[which.max(steps.interval$steps)]]
```
5-minute interval containing max steps: 835

## Imputing missing values
Note that there are a number of days/intervals where there are missing values (coded as NA). The presence of missing days may introduce bias into some calculations or summaries of the data.

1. Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)

```r
num.na <- sum(!complete.cases(data))
num.rows <- dim(data)[1]
```
The total number of rows with NAs: 2304  
The total number of rows: 17568  
Missing Ratio: 13.11%

2. Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.
3. Create a new dataset that is equal to the original dataset but with the missing data filled in.

```r
mu0 <- mean(data$steps, na.rm=TRUE)
data$steps[is.na(data$steps)] <- mu0
```

4. Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?

```r
steps.day2 <-
    data %>%
    group_by(date) %>%
    summarize(steps=sum(steps))

hist(steps.day2$steps, xlab='Steps',
     main='Histogram of Steps taken each day')
```

![](PA1_template_files/figure-html/unnamed-chunk-9-1.png) 

```r
mu2 <- mean(steps.day2$steps, na.rm=TRUE)
med2 <- median(steps.day2$steps, na.rm=TRUE)
```
the total number of steps taken per day(with missing value)

* mean: 10766.19.
* median: 10765.

the total number of steps taken per day(with filled value)

* mean: 10766.19.
* median: 10766.19.

The mean value doesn't change.  
The median has changed.

## Are there differences in activity patterns between weekdays and weekends?
For this part the weekdays() function may be of some help here. Use the dataset with the filled-in missing values for this part.

1. Create a new factor variable in the dataset with two levels ??? <U+201C>weekday<U+201D> and <U+201C>weekend<U+201D> indicating whether a given date is a weekday or weekend day.

```r
Sys.setlocale('LC_ALL', 'C')
```

```
## [1] "C"
```

```r
weekend <- weekdays(as.Date(data$date)) %in% c('Saturday', 'Sunday')
data$dayofweek <- factor(weekend, labels=c('weekday', 'weekend'))
```

2. Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). See the README file in the GitHub repository to see an example of what this plot should look like using simulated data.


```r
steps.weekday <-
    data %>%
    filter(dayofweek=='weekday') %>%
    group_by(interval) %>%
    summarize(steps=mean(steps, na.rm=TRUE))
steps.weekend <-
    data %>%
    filter(dayofweek=='weekend') %>%
    group_by(interval) %>%
    summarize(steps=mean(steps, na.rm=TRUE))

rng <- range(steps.weekday$steps, steps.weekend$steps)

par(mfrow=c(2,1))
with(steps.weekday,
     plot(interval, steps, type='l', ylim=rng, main='Weekday')
     )
with(steps.weekend,
     plot(interval, steps, type='l', ylim=rng, main='Weekend')
     )
```

![](PA1_template_files/figure-html/unnamed-chunk-11-1.png) 

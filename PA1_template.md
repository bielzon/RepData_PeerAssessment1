# Reproducible Research: Peer Assessment 1


## Loading and preprocessing the data
Show any code that is needed to  
1. Load the data (i.e. read.csv())  
2. Process/transform the data (if necessary) into a format suitable for your analysis.

```r
unzip("activity.zip")
activity <- read.csv("activity.csv")
```

## What is mean total number of steps taken per day?
For this part of the assignment, you can ignore the missing values in the dataset.

1. Calculate the total number of steps taken per day.


```r
steps_bydate <- aggregate(steps ~ date, data = activity, FUN = sum)
```
2. If you do not understand the difference between a histogram and a barplot, research the difference between them. Make a histogram of the total number of steps taken each day.


```r
hist(steps_bydate$steps, breaks = 20,
     main = "Histogram of the Total Number of Steps Taken Each Day",
     xlab = "Number of Steps", col = "green", las = 1)
```

![](PA1_template_files/figure-html/unnamed-chunk-3-1.png)<!-- -->

3. Calculate and report the mean and median of the total number of steps taken per day


```r
mean(steps_bydate$steps)
```

```
## [1] 10766.19
```

```r
median(steps_bydate$steps)
```

```
## [1] 10765
```


## What is the average daily activity pattern?
1. Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)


```r
steps_byinterval <- aggregate(steps ~ interval, data = activity, FUN = mean)
plot(steps_byinterval, type = "l",
     main = "Time Series of the Average Number of Steps Taken",
     ylab = "Average Number of Steps Taken Across Days",
     xlab = "5-minute Interval", las = 1)
```

![](PA1_template_files/figure-html/unnamed-chunk-5-1.png)<!-- -->

2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

```r
steps_byinterval[steps_byinterval$steps == max(steps_byinterval$steps),]
```

```
##     interval    steps
## 104      835 206.1698
```


## Imputing missing values
Note that there are a number of days/intervals where there are missing values (coded as NA). The presence of missing days may introduce bias into some calculations or summaries of the data.

1. Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)


```r
sum(is.na(activity))
```

```
## [1] 2304
```

2. Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.
3. Create a new dataset that is equal to the original dataset but with the missing data filled in.

```r
activity2 <- activity
activity2$steps[which(is.na(activity2))] <- median(activity2$steps, na.rm = TRUE)
```

4. Make a histogram of the total number of steps taken each day and Calculate and report the **mean** and **median** total number of steps taken per day.

```r
daymean <- aggregate(steps ~ date, data = activity2, FUN = sum)
hist(daymean$steps, breaks = 20,
     main = paste("Histogram of the Total Number of Steps Taken Each Day",
                  "\n(Missing Values Imputed as the Median)"),
     xlab = "Number of Steps", col = "green", las = 1)
```

![](PA1_template_files/figure-html/unnamed-chunk-9-1.png)<!-- -->

```r
print(mean(daymean$steps))
```

```
## [1] 9354.23
```

```r
print(median(daymean$steps))
```

```
## [1] 10395
```
*Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?*  
The values differ from the first part of the assignment, which lead to differences in the estimates of the total daily number of steps, depending on the imputing strategy used.

## Are there differences in activity patterns between weekdays and weekends?
For this part the weekdays() function may be of some help here. Use the dataset with the filled-in missing values for this part.

1. Create a new factor variable in the dataset with two levels - "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.

```r
activity2 <- activity
activity2$steps[which(is.na(activity2))] <- median(activity2$steps, na.rm = TRUE)
activity2$type <- as.factor(sapply(activity2$date, function(date) {
    if(weekdays(as.Date(date)) %in% c("sábado", "domingo"))
        day <- "weekend"
    else
        day <- "weekday"
}))
```

2. Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). See the README file in the GitHub repository to see an example of what this plot should look like using simulated data.

```r
par(mfrow = c(2,1), mar = c(3,2,1,3), mgp=c(.5,1,0))
weekend <- aggregate(steps ~ interval, data = activity2, FUN = mean,
                     subset = activity2$type == "weekend")
plot(weekend, type = "l", col = "blue", xlab = "Weekend Interval", ylab = "Number of Steps", axes = FALSE)
axis(4)
box()
weekday <- aggregate(steps ~ interval, data = activity2, FUN = mean,
                     subset = activity2$type == "weekday")
plot(weekday, type = "l", col = "blue", xlab = "Weekday Interval", ylab = "Number of Steps", axes = FALSE)
axis(4)
axis(3)
box()
```

![](PA1_template_files/figure-html/unnamed-chunk-11-1.png)<!-- -->

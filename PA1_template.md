---
title: "PA1_template.Rmd"
output: html_document
---
## Loading and preprocessing the data
The code below will load the data and asign the correct class to each column i.e. integer, date, ineteger

```r
data <-read.csv("./activity.csv", colClasses = c("integer","Date","integer"))
```

## What is mean total number of steps taken per day?
First we aggregate the data to show the total number of steps per day, this will be stored in aggdata
1. Make a histogram of aggdata
2. Calculate the mean and median og aggdata

```r
aggdata <-aggregate(steps ~ date,data=data,sum)
hist(aggdata$steps)
```

![plot of chunk unnamed-chunk-2](figure/unnamed-chunk-2-1.png) 

```r
mean(aggdata$steps)
```

```
## [1] 10766.19
```

```r
median (data$steps, na.rm= TRUE)
```

```
## [1] 0
```

## What is the average daily activity pattern?
First we aggregate the original data to show the average number of steps per interval (across all dates). this is stored in aggdatainternvals
1. Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken (y-axis) using aggdataintervals
2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps? - > find the max number under the steps column in the aggdataintervals and assign it along with its related interval to q2. Print the interval value for q2


```r
aggdataintervals <-aggregate(steps ~ interval ,data=data,mean)
plot(aggdataintervals$interval, aggdataintervals$steps, type = "l")
```

![plot of chunk unnamed-chunk-3](figure/unnamed-chunk-3-1.png) 

```r
q2 <-subset(aggdataintervals,steps == max(aggdataintervals$steps))
q2[,1]
```

```
## [1] 835
```

## Imputing missing values
Note that there are a number of days/intervals where there are missing values (coded as NA). The presence of missing days may introduce bias into some calculations or summaries of the data.
1. Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs) - calculated this using total number of rows in raw data minus the complete cases (non-NAs)
2. Devise a strategy for filling in all of the missing values in the dataset. The strategy I have used is to use zero to fill in the NAs which is also the median of the data.
3. Created a new dataset called noNAdata and filled it will all the original data, then replaced all NAs with zero, summary of noNAdata demonstrates the results
4. Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps? Yes, there are more zeros


```r
nrow(data)-sum(complete.cases(data))
```

```
## [1] 2304
```

```r
noNAdata <- as.data.frame(data)
noNAdata[is.na(noNAdata)] <- 0
summary(noNAdata)
```

```
##      steps             date               interval     
##  Min.   :  0.00   Min.   :2012-10-01   Min.   :   0.0  
##  1st Qu.:  0.00   1st Qu.:2012-10-16   1st Qu.: 588.8  
##  Median :  0.00   Median :2012-10-31   Median :1177.5  
##  Mean   : 32.48   Mean   :2012-10-31   Mean   :1177.5  
##  3rd Qu.:  0.00   3rd Qu.:2012-11-15   3rd Qu.:1766.2  
##  Max.   :806.00   Max.   :2012-11-30   Max.   :2355.0
```

```r
hist(noNAdata$steps)
```

![plot of chunk unnamed-chunk-4](figure/unnamed-chunk-4-1.png) 

```r
mean(noNAdata$steps)
```

```
## [1] 32.47996
```

```r
median(noNAdata$steps)
```

```
## [1] 0
```

## Are there differences in activity patterns between weekdays and weekends?
For this part the weekdays() function may be of some help here. 
Using the noNAdata
1. Create a new factor variable in the dataset with two levels – “weekday” and “weekend” indicating whether a given date is a weekday or weekend day - first created a new column called weekday which shows the day and then another column called isweekday to show whether the day is w/e or not.
2. Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis) - I have used the lattice package for this.

```r
noNAdata$weekday <- weekdays(noNAdata$date)
noNAdata$isweekday <- ifelse(noNAdata$weekday %in% c("Saturday", "Sunday"), "weekend", "weekday")
library(lattice)
f <- factor(noNAdata$isweekday)
xyplot(noNAdata$steps ~ noNAdata$interval | f, layout = c(1,2), type = "l")
```

![plot of chunk unnamed-chunk-5](figure/unnamed-chunk-5-1.png) 

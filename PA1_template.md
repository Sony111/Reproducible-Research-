# Activity Report
Author    : Usha
Date      : Sept 14,2014

### Loading and preprocessing the data
#### 1. Load the data (i.e. read.csv())

#### 2. Process/transform the data (if necessary) into a format suitable for your analysis

```r
library(ggplot2)
```

```
## Warning: package 'ggplot2' was built under R version 3.1.1
```

```r
activity <- read.csv("activity.csv",header=T)
activity$date <-as.Date(activity$date)
act <- activity
```

### What is mean total number of steps taken per day?
* For this part of the assignment, you can ignore the missing values in the dataset. *

#### 1. Make a histogram of the total number of steps taken each day


```r
activity_S <- aggregate(activity$steps,list(activity$date),sum)
hist(activity_S$x,xlab="steps",main="Total No.of Steps Each Day")
```

![plot of chunk unnamed-chunk-2](figure/unnamed-chunk-2.png) 

#### 2. Calculate and report the mean and median total number of steps taken per day

```r
activity_Sum <- aggregate(activity$steps,list(activity$date,activity$interval),sum)
activity_Mean <- aggregate(activity$steps,list(activity$date),mean)
activity_Median <- aggregate(activity$steps,list(activity$date),median)
```


#### activity_Mean contains the mean for the total No.of steps taken per day 

#### activity_Medain contains the median for the total No.of steps taken per day is either 0 or NA

### What is the average daily activity pattern?

#### 1. Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)



```r
plot(activity_Mean$Group.1,activity_Mean$x,main="Mean & Median(total number of steps taken per day)",ylab="Steps",xlab="Oct & Nov Month",col="blue",type="l")
abline(h=activity_Median$x,col="red")
legend("topright",col = c("blue", "red"),legend = c("Mean", "Median"), lty=1)
```

![plot of chunk unnamed-chunk-4](figure/unnamed-chunk-4.png) 

#### 2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?


```r
max(activity_Sum$x,na.rm=TRUE) #21194
```

```
## [1] 806
```

```r
max <- subset(activity,date == "2012-11-23",select=c(steps,interval,date))
```

### 2355 is the interval on average across all days in the dataset contains the maximum number of steps 21194 on 2012-11-23.

### Imputing missing values

#### Note that there are a number of days/intervals where there are missing values (coded as NA). The presence of missing days may introduce bias into some calculations or summaries of the data.

#### 1. Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)


```r
nrow(act)
```

```
## [1] 17568
```

```r
nrow(na.omit(act))
```

```
## [1] 15264
```

### Total Number of records present in the dataset are 17568.When we omit the NA values(2304)records then we got 15264 records in the dataset.

#### 2. Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.


```r
nsum<- sum(na.omit(activity_Sum$x))
nsum/nrow(na.omit(act))  #  for steps -37.38  sum of steps/no.of records
```

```
## [1] 37.38
```

```r
miss<- which(is.na(act$steps))
act$steps[miss] <- 37.38
```

### I replaced NA with 37.38 for steps


#### 3.Create a new dataset that is equal to the original dataset but with the missing data filled in.

### Created a new dataset that is equal to the original dataset but with the missing data filled with 37.38


#### 4.Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?


```r
act_Sum <- aggregate(act$steps,list(act$date),sum)
hist(act_Sum$x,xlab="steps",main="Total No.of Steps Each Day")
```

![plot of chunk unnamed-chunk-8](figure/unnamed-chunk-81.png) 

```r
act_Mean <- aggregate(act$steps,list(act$date),mean)
act_Median <- aggregate(act$steps,list(act$date),median)
plot(act_Mean$Group.1,act_Mean$x,main="Mean & Median(total number of steps taken per day)",ylab="Steps",xlab="Oct & Nov Month",col="blue",type="l")
abline(h=act_Median$x,col="red")
legend("topright",col = c("blue", "red"),legend = c("Mean", "Median"), lty=1)
```

![plot of chunk unnamed-chunk-8](figure/unnamed-chunk-82.png) 

### Yes. If the data is changed the graph will be changed.Due to missing data graph is not continuous (there will be lot of gaps in between)

### Are there differences in activity patterns between weekdays and weekends?
### For this part the weekdays() function may be of some help here. Use the dataset with the filled-in missing values for this part.
#### 1. Create a new factor variable in the dataset with two levels - "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.
 

```r
 d <- weekdays(act$date)
act <- data.frame(act,d) # inserting a column 
colnames(act)[4] <- "day"

 act$day<- gsub("^[S].*","Weekend",act$day)
act$day<- gsub("^[M].*day","Weekday",act$day)
act$day<- gsub("^[Tu].*day","Weekday",act$day)
act$day<- gsub("^[W].*day","Weekday",act$day)
act$day<- gsub("^[Th].*day","Weekday",act$day)
act$day<- gsub("^[F].*day","Weekday",act$day)
act <- transform(act,day=factor(act$day))
```

#### 2. Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). The plot should look something like the following, which was creating using simulated data:
  
##### Your plot will look different from the one above because you will be using the activity monitor data. Note that the above plot was made using the lattice system but you can make the same version of the plot using any plotting system you choose.


```r
actSI <- aggregate(act$steps,list(act$date,act$day,act$interval),sum)
colnames(actSI) <- c("date","day","interval","steps")

qplot(interval,steps, data = actSI , geom="line" , facets = ~day)
```

![plot of chunk unnamed-chunk-10](figure/unnamed-chunk-10.png) 

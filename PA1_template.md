
Reproducible Research: Peer Assessment 1
Pinecode99

October 23, 2016

Loading and preprocessing the data
Assuming you have manually downloaded the data, you can load it with the following R code:

library(dplyr)
## Warning: package 'dplyr' was built under R version 3.2.5
## 
## Attaching package: 'dplyr'
## The following objects are masked from 'package:stats':
## 
##     filter, lag
## The following objects are masked from 'package:base':
## 
##     intersect, setdiff, setequal, union
library(lubridate)
## Warning: package 'lubridate' was built under R version 3.2.5
## 
## Attaching package: 'lubridate'
## The following object is masked from 'package:base':
## 
##     date
act1 <- read.csv("./activity.csv", header = TRUE, sep = ",")
What is mean total number of steps taken per day?
Part 1 answer. Total steps taken per day is calculated using:

act2 <- aggregate(steps~date,act1,sum)
Part 2 Answer Use 15 bins (breaks) to display adequately:

hist(act2$steps,breaks=15, main="Histogram of mean steps/day", xlab="Mean steps/day")


Part 3 answer Create a mean file and median file then combine them

act3 <- as.data.frame(mean(act2$steps))
names(act3) <- "mean"
act4 <- as.data.frame(median(act2$steps))
names(act4) <- "median"
act5 <- cbind(act3, act4)
act5
##       mean median
## 1 10766.19  10765
# rm(act2,act3,act4)
What is the average daily activity pattern?
Make a time series plot (i.e. type = “l” ) of the 5-minute interval (x-axis) and the average

act10 <- aggregate(steps~interval,act1,mean)
plot(steps~interval,data=act10, type="l", xlab="Interval", main="Average number of steps taken by interval (Oct-Nov 2012)")


Which 5-minute interval, on average across all the days in the dataset,
contains the maximum number of steps?

MaxInt1 <- which.max(act10$steps)
MaxInt2 <- act10[MaxInt1,]
MaxInt2
##     interval    steps
## 104      835 206.1698
Imputing missing values
Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NA’s)

sum(is.na(act1[,1])) #2304
## [1] 2304
sum(is.na(act1[,2])) #0
## [1] 0
sum(is.na(act1[,3])) #0
## [1] 0
Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. My strategy will be to use the mean of the relevant segments.

Create a new dataset that is equal to the original dataset but with the missing data filled in. create a new copy from act1 to work with

act30 <- act1
rm(act1)

for (i in 1:nrow(act30))
{
       if (is.na(act30$steps[i]))
       {
         #build code to lookup mean data based on the interval
         interval1 <- act30[i,3]
         interval2 <- which(act10$interval==interval1)
         meansteps <- act10[interval2,2]
         act30[i,1] <- meansteps
       }
        
}

rm(act10)
# sum(is.na(act30[,1])) # demosstrates the NA's have been replaced
Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?

act31 <- aggregate(steps~date,act30,sum)
hist(act31$steps,breaks=15, main="Histogram of mean steps/day", xlab="Mean steps/day")


create a file for mean and one for median and combine them

act32 <- as.data.frame(mean(act31$steps))
names(act32) <- "mean"
act33 <- as.data.frame(median(act31$steps))
names(act33) <- "median"
act34 <- cbind(act32, act33)
this is the result (coincidentally! (or not) the mean and median are forced to be the same using the method choosen) this can be demonstrated by sorting the act31 column by steps

act34
##       mean   median
## 1 10766.19 10766.19
rm(act31,act32,act33,act34)
answer: the results differ by a small amout. Using averaged data for the missing data ‘tightened up’ the histogram making the data more centrally located than would have been the case if we had the real data.

Are there differences in activity patterns between weekdays and weekends?
For this part the weekdays() function may be of some help here. Use the dataset with the filled in missing values for this part

Create a new factor variable in the dataset with two levels weekday and weekend indicating whether a given date is a weekday or weekend day.

convert the date filed from factor to date

act40 <- act30
rm(act30)
act40$date <- as.Date(act40$date)
act41 <- mutate(act40, daycode = weekdays(date))
act42 <- mutate(act41,code="a")

for (i in 1:nrow(act42))
{
        if (act42$daycode[i] == "Saturday" | act42$daycode[i] == "Sunday")
        {
               act42$code[i] <- "weekend"
        } 
        else
        {
                act42$code[i] <- "weekday"
        }
        
}
head(act42,5)
##       steps       date interval daycode    code
## 1 1.7169811 2012-10-01        0  Monday weekday
## 2 0.3396226 2012-10-01        5  Monday weekday
## 3 0.1320755 2012-10-01       10  Monday weekday
## 4 0.1509434 2012-10-01       15  Monday weekday
## 5 0.0754717 2012-10-01       20  Monday weekday
rm(act41)
par(mfrow=c(2,1))

### a plot for weekdays
act50 <- filter(act42,code=="weekday")
act51 <- aggregate(steps~interval,act50,mean)
plot(steps~interval,data=act51, type="l", xlab="Interval", main="Average number of steps (weekday)")

### a plot for weekends
act55 <- filter(act42,code=="weekend")
act56 <- aggregate(steps~interval,act55,mean)
plot(steps~interval,data=act56, type="l", xlab="Interval", main="Average number of steps (weekend)")


###dev.off()

rm(act40,act42,act50,act51,act55,act56)






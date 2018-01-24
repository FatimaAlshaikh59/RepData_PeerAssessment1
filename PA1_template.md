Loading and preprocessing the data
----------------------------------

The file wil be
[downloaded](d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip),
unzipped and saved in the directory. The data will be read as .csv file,
the date varible class will be changed into Date and a column determing
the weekday will be added.

    if(!file.exists("./Project")){dir.create("./Project")}
    fileUrl <- "https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip"
    download.file(fileUrl,destfile="./Project/repdata.zip")
    unzip(zipfile="./Project/repdata.zip",exdir="./Project")
    mydata<- read.csv("./Project/activity.csv")
    head(mydata)

    ##   steps       date interval
    ## 1    NA 2012-10-01        0
    ## 2    NA 2012-10-01        5
    ## 3    NA 2012-10-01       10
    ## 4    NA 2012-10-01       15
    ## 5    NA 2012-10-01       20
    ## 6    NA 2012-10-01       25

What is mean total number of steps taken per day?
-------------------------------------------------

We will calculate the total number of steps taken per day, Make a
histogram of the total number of steps taken each day and Calculate and
report the mean and median of the total number of steps taken per day

    histdata<- aggregate(steps~ date, mydata, sum)
    head(histdata)

    ##         date steps
    ## 1 2012-10-02   126
    ## 2 2012-10-03 11352
    ## 3 2012-10-04 12116
    ## 4 2012-10-05 13294
    ## 5 2012-10-06 15420
    ## 6 2012-10-07 11015

    hist(histdata$steps, breaks = 53, xlab = "Total number of steps taken each day", main = "Histogram of the total number of steps taken each day")

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-2-1.png)

    mean<- mean(histdata$steps)
    median<- median(histdata$steps)
    mean

    ## [1] 10766.19

    median

    ## [1] 10765

What is the average daily activity pattern?
-------------------------------------------

We will Make a time series plot (i.e. type = "l") of the 5-minute
interval (x-axis) and the average number of steps taken, averaged across
all days (y-axis) and determine the 5 min interval (on average) with
maximum number of steps.

    plotdata<- aggregate(steps~ interval, mydata, FUN = function(mydata) c(mean= mean(mydata))
    )
    plot(plotdata$interval, plotdata$steps, type = "l", xlab = "interval", ylab = "number of steps", main = "average daily activity pattern")

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-3-1.png)

    maximum<- plotdata[which.max(plotdata$steps),]
    maximum

    ##     interval    steps
    ## 104      835 206.1698

Imputing missing values
-----------------------

The missing value will be imputed using mice package, and examine the
difference before and after imputing

    newdata<- mice(mydata)

    ## 
    ##  iter imp variable
    ##   1   1  steps
    ##   1   2  steps
    ##   1   3  steps
    ##   1   4  steps
    ##   1   5  steps
    ##   2   1  steps
    ##   2   2  steps
    ##   2   3  steps
    ##   2   4  steps
    ##   2   5  steps
    ##   3   1  steps
    ##   3   2  steps
    ##   3   3  steps
    ##   3   4  steps
    ##   3   5  steps
    ##   4   1  steps
    ##   4   2  steps
    ##   4   3  steps
    ##   4   4  steps
    ##   4   5  steps
    ##   5   1  steps
    ##   5   2  steps
    ##   5   3  steps
    ##   5   4  steps
    ##   5   5  steps

    summary(newdata)

    ## Multiply imputed data set
    ## Call:
    ## mice(data = mydata)
    ## Number of multiple imputations:  5
    ## Missing cells per column:
    ##    steps     date interval 
    ##     2304        0        0 
    ## Imputation methods:
    ##    steps     date interval 
    ##    "pmm"       ""       "" 
    ## VisitSequence:
    ## steps 
    ##     1 
    ## PredictorMatrix:
    ##          steps date interval
    ## steps        0    1        1
    ## date         0    0        0
    ## interval     0    0        0
    ## Random generator seed value:  NA

    newdata<- complete(newdata)
    histnewdata<- aggregate(steps~ date, newdata, sum)
    par(mfrow = c(1,2))
    hist(histnewdata$steps, breaks = 53, xlab = "Total number of steps taken each day", main = "Histogram of imputed data")
    hist(histdata$steps, breaks = 53, xlab = "Total number of steps taken each day", main = "Histogram of original data")

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-5-1.png)

    mean1<- mean(histnewdata$steps)
    median1<- median(histnewdata$steps)
    mean1

    ## [1] 10773.33

    median1

    ## [1] 10765

    difference_in_mean<- mean- mean1
    difference_in_median<- median- median1
    difference_in_mean

    ## [1] -7.13919

    difference_in_median

    ## [1] 0

Are there differences in activity patterns between weekdays and weekends?
-------------------------------------------------------------------------

    newdata$date<- as.Date(newdata$date)
    newdata$weekday<- weekdays(newdata$date)
    Weekends<- subset(newdata, weekday==c("Saturday", "Sunday"))
    Weekdays<- subset(newdata, weekday!=c("Saturday", "Sunday"))
    plotweekends<- aggregate(steps~ interval, Weekends, FUN = function(Weekends) c(mean= mean(Weekends)))
    plotweekdays<- aggregate(steps~ interval, Weekdays,  FUN = function(Weekdays) c(mean= mean(Weekdays)))
    par(mfrow= c(1,2))

    plot(plotweekdays$interval, plotweekdays$steps, type = "l", xlab = "interval", ylab = "number of steps", main = "Weekdays")
    plot(plotweekends$interval, plotweekends$steps, type = "l", xlab = "interval", ylab = "number of steps", main = "Weekends")    

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-6-1.png)

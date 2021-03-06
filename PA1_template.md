# Reproducible Research: Peer Assessment 1


## Loading and preprocessing the data

Loading Data...
Data refers to an activity log, number of steps for each 5 minutes.


```r
data <- read.csv(unz("activity.zip", "activity.csv"), header=T, quote="\"", sep=",")
```



## What is mean total number of steps taken per day?

For getting the total number of steps:
- Begin with  discarding NA values in a new dataset "dataNoNA"
- Aggregating by date the sum of steps of each day in a new dataset stepsEachDay
- Displaying the histogram of frequency of dum of steps for each day.


```r
dataNoNa<-data[(!is.na(data$steps)),]
stepsEachDay<-aggregate(list(steps=dataNoNa$steps),by=list(date=dataNoNa$date),FUN=sum)
hist(stepsEachDay$steps,xlab="Number of steps in a day",main="Total number of steps each day")
```

![](./PA1_template_files/figure-html/unnamed-chunk-2-1.png) 

The mean and median for the "stepsEachDay" dataset:


```r
mean(stepsEachDay$steps)
```

```
## [1] 10766.19
```

```r
median(stepsEachDay$steps)
```

```
## [1] 10765
```

## What is the average daily activity pattern?

We calculate the mean for each period of 5 minutes for all the days 

"dailyAverage" data set gathers this dataset-wide average, grouped  for each 5 minute interval.
The number of steps for each interval is also plotted.


```r
dailyAverage<-aggregate(x=list(steps=dataNoNa$steps),by=list(interval=dataNoNa$interval),FUN=mean)
plot(dailyAverage$interval,dailyAverage$steps,type="l", xlab="Five Minute intervals", ylab="Steps")
```

![](./PA1_template_files/figure-html/unnamed-chunk-4-1.png) 


The interval with most steps :

```r
dailyAverage[which.max(dailyAverage$steps),]
```

```
##     interval    steps
## 104      835 206.1698
```

## Imputing missing values

For the purpose of evaluating the impact of missing values , it is prepared a sustitution of the NA's with the mean dataset-wide for the corresponding period.
The function "valor" is prepared so in conjunction with "sapply" NA's would be substituted in a new dataset "dataNaIsAvrg".


```r
dataNaIsAvrg<-data

valor<-function(x){
                   val<-(dailyAverage[dailyAverage$interval==x,"steps"])
                   return(as.integer(val))
                   }
```

Filling , using "sapply" the average interval value on NA's


```r
dataNaIsAvrg[is.na(dataNaIsAvrg$steps),]$steps<-sapply(dataNaIsAvrg[is.na(dataNaIsAvrg$steps),]$interval,valor)
```

We prepare same historgram done before: "Total number of steps each day" with the NA filled dataset.

First aggregating grouping by date


```r
dailyFillSteps<-aggregate(x=list(steps=dataNaIsAvrg$steps),by=list(interval=dataNaIsAvrg$date),FUN=mean)
```


Then displaying the histogram and giving Mean and Median.

```r
hist(dailyFillSteps$steps,xlab="Number of steps in a day",main="Total number of steps each day , with mean instead of NA")
```

![](./PA1_template_files/figure-html/unnamed-chunk-9-1.png) 

```r
mean(dailyFillSteps$steps)
```

```
## [1] 37.32559
```

```r
median(dailyFillSteps$steps)
```

```
## [1] 36.94792
```
## Are there differences in activity patterns between weekdays and weekends?

For implementing this comparison , the following task should be accomplished:

- A weekday field , should be added to the dataset.
- A new dataset for weekends should be obtained and its corresponding interval step-average dataset.
- A new dataset for working weekdays also should be obtained and its corresponding interval step-average dataset.
- With both datasets perform and display the plots describing activity patterns.

First we add a field for weekday


```r
dataNaIsAvrg$date<-as.Date(dataNaIsAvrg$date)
dataNaIsAvrg$day<-weekdays(dataNaIsAvrg$date)
```

Then the weekend dataset is derived and its corresponding weekend interval-step average "weekendavrg"


```r
weekDayAverage<-aggregate(steps ~ interval + day,data=dataNaIsAvrg,FUN=mean)
weekend<-weekDayAverage[(weekDayAverage$day %in% c("Saturday", "Sunday")),]
weekendavrg<-aggregate(x=list(steps=weekend$steps),by=list(interval=weekend$interval),FUN=mean)
```

Also the  working weekday dataset is derived and its corresponding interval-step average "weekavrg"

```r
week<-weekDayAverage[(!(weekDayAverage$day %in% c("Saturday", "Sunday"))),]
weekavrg<-aggregate(x=list(steps=week$steps),by=list(interval=week$interval),FUN=mean)
```

And the comparison is ready to be ploted

```r
plot(weekendavrg$interval,weekendavrg$steps,type="l",xlab="5 Minute interval for weekend days",ylab="Steps")
```

![](./PA1_template_files/figure-html/unnamed-chunk-13-1.png) 

```r
plot(weekavrg$interval,weekavrg$steps,type="l",xlab="5 Minute interval for working days",ylab="Steps")
```

![](./PA1_template_files/figure-html/unnamed-chunk-13-2.png) 

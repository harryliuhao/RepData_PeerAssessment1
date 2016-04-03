<<<<<<< HEAD
---
title: "Reproducible Research - week one project assignment"
author: "Hao Liu"
date: "April 2, 2016"
output: html_document
---

This is an R Markdown document. Markdown is a simple formatting syntax for authoring HTML, PDF, and MS Word documents. For more details on using R Markdown see <http://rmarkdown.rstudio.com>.

```{r setup}
require(knitr)
opts_knit$set(root.dir = 'C:/Users/Hao/Documents/1_r_projects/t5')

```

*Assignment requirements:*
1.Code for reading in the dataset and/or processing the data

```{r}
raw.data<-read.csv("activity.csv", header=T)
head(raw.data)
df.sum<-aggregate(steps ~ date, data=raw.data, sum)
```

2.Histogram of the total number of steps taken each day

```{r}
hist(df.sum$steps, breaks=nrow(df.sum), main="Histogram of Daily Steps", xlab="Number of Daily Steps Taken", ylab="Number of Days")

```

3.Mean and median number of steps taken each day  
*Note that the assignment is not asking for the median of steps in each day, which will be zero because of the large number of intervals that do not have a value. This is asking for the median of the daily totals for the date period 2012-10-01 through 2012-11-30*
```{r}
old.mean<-mean(df.sum$steps)
old.median<-median(df.sum$steps)
#min(as.Date(raw.data$date,format="%Y-%m-%d"))
```

4.Time series plot of the average number of steps taken
```{r}
df.mean<-aggregate(steps ~ interval, data=raw.data, mean)
plot(df.mean$steps~df.mean$interval,type="l",main="average number of steps taken by interval", xlab="Interval", ylab="average number of steps taken")

```

5.The 5-minute interval that, on average, contains the maximum number of steps  
*the following code may seem confusing as it is a two step process. The row() step gets the row number of the max value, so its interval can be retrieved.*

```{r}
df.mean[row(df.mean)[df.mean==max(df.mean$steps)],]$interval
```

6.Code to describe and show a strategy for imputing missing data
```{r}
nrow(raw.data)
nrow(raw.data[is.na(raw.data$steps),])
nrow(raw.data[is.na(raw.data$steps),])/nrow(raw.data)
```
NA in steps is 13% of all raw data, which is significant. In practice, that usually means the value is zero. However, the intention of the assigment here is to replace NA with a non-zero value, so we will use the mean value for the day to replace the NA.

```{r}
new.data<-raw.data

#separate mean.data is needed for aggregate, otherwise, 2012-10-01 and 2012-11-30 which two days are all NA in "steps" column will result in missing dates in the daily.mean data.It really doesn't matter for the end results as NA and zero have the same effect. I am only doing this for the project assignment's requirements, which ask all NA to be removed.  
mean.data<-raw.data 
mean.data$steps<-ifelse(is.na(mean.data$steps),0,mean.data$steps) 
daily.mean<-aggregate(steps ~ date, data=mean.data, mean,na.rm = T)
colnames(daily.mean)[2]<-"step.mean"

new.data<-merge(new.data,daily.mean,by="date")

new.data$steps<- ifelse(is.na(new.data$steps), new.data$step.mean, new.data$steps)
#the following code was the code that didn't work. by moving is.na() to the right and combined with ifelse(), it forced the condition to be applied at row (vector) level. When it was on the right in the wrong code, it was a subsetting condition.
#new.data[is.na(new.data$steps),]$steps<- new.data$step.mean

```

7.Histogram of the total number of steps taken each day after missing values are imputed
```{r}
new.sum<-aggregate(steps ~ date, data=new.data, sum)
hist(new.sum$steps, breaks=nrow(new.sum), main="Histogram of Daily Steps with imputed value", xlab="Number of Daily Steps Taken", ylab="Number of Days")

```
Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?
```{r}
new.mean<-mean(new.sum$steps)
new.median<-median(new.sum$steps)
step.summary<-rbind(c(old.mean,old.median),c(new.mean,new.median))
rownames(step.summary)<-c("old","new")
colnames(step.summary)<-c("mean","median")
step.summary
```
To answer the questions: the values are different, and the imputing missing data has decreased the mean and median values.  

8.Panel plot comparing the average number of steps taken per 5-minute interval across weekdays and weekends
```{r}
new.data$wday<-weekdays(as.Date(new.data$date,format="%Y-%m-%d"))
new.data$daytype<-ifelse(new.data$wday %in% c("Saturday", "Sunday"), "weekend","weekday")

wday.mean<-aggregate(steps ~ interval+daytype, data=new.data, mean)

plot(df.mean$steps~df.mean$interval,type="l",main="average number of steps taken by interval", xlab="Interval", ylab="average number of steps taken")

library(ggplot2)
library(ggthemes)
g<-ggplot(wday.mean, aes(x=interval, y=steps))
g+geom_line()+facet_grid(daytype~.)+labs(x="Interval", y="Steps")

```
  
This chart tells us in weekdays more activities concentrted in the morning rush, then during work hours, people don't move much. During the weekend, there is less a morning rush, but more activities during day light hours. 



=======
## Introduction

As this is a reproducible research, all analysis has been documented in the PA1_template.html file. It is not necessary to re-summarize the analysis in this readme file.
>>>>>>> origin/master

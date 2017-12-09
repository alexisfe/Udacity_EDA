---
title: "One Variable"
output: 
  html_document: 
    keep_md: yes
---

Import necessary libraries

```r
library(RCurl)
library(ggplot2)
library(gridExtra)
```

Setting seed for reproducibility

```r
set.seed(1234)
```

Load pseudo facebook data set

```r
destfile <- "input/pseudo_facebook.tsv"

if(!file.exists(destfile)){
  download.file("https://s3.amazonaws.com/udacity-hosted-downloads/ud651/pseudo_facebook.tsv",destfile=destfile,method="libcurl")
}
pseudo_facebook <- read.csv(file=destfile, sep="\t")
```

Visualize data set structure and sample

```r
str(pseudo_facebook)
```

```
## 'data.frame':	99003 obs. of  15 variables:
##  $ userid               : int  2094382 1192601 2083884 1203168 1733186 1524765 1136133 1680361 1365174 1712567 ...
##  $ age                  : int  14 14 14 14 14 14 13 13 13 13 ...
##  $ dob_day              : int  19 2 16 25 4 1 14 4 1 2 ...
##  $ dob_year             : int  1999 1999 1999 1999 1999 1999 2000 2000 2000 2000 ...
##  $ dob_month            : int  11 11 11 12 12 12 1 1 1 2 ...
##  $ gender               : Factor w/ 2 levels "female","male": 2 1 2 1 2 2 2 1 2 2 ...
##  $ tenure               : int  266 6 13 93 82 15 12 0 81 171 ...
##  $ friend_count         : int  0 0 0 0 0 0 0 0 0 0 ...
##  $ friendships_initiated: int  0 0 0 0 0 0 0 0 0 0 ...
##  $ likes                : int  0 0 0 0 0 0 0 0 0 0 ...
##  $ likes_received       : int  0 0 0 0 0 0 0 0 0 0 ...
##  $ mobile_likes         : int  0 0 0 0 0 0 0 0 0 0 ...
##  $ mobile_likes_received: int  0 0 0 0 0 0 0 0 0 0 ...
##  $ www_likes            : int  0 0 0 0 0 0 0 0 0 0 ...
##  $ www_likes_received   : int  0 0 0 0 0 0 0 0 0 0 ...
```

```r
head(pseudo_facebook)
```

```
##    userid age dob_day dob_year dob_month gender tenure friend_count
## 1 2094382  14      19     1999        11   male    266            0
## 2 1192601  14       2     1999        11 female      6            0
## 3 2083884  14      16     1999        11   male     13            0
## 4 1203168  14      25     1999        12 female     93            0
## 5 1733186  14       4     1999        12   male     82            0
## 6 1524765  14       1     1999        12   male     15            0
##   friendships_initiated likes likes_received mobile_likes
## 1                     0     0              0            0
## 2                     0     0              0            0
## 3                     0     0              0            0
## 4                     0     0              0            0
## 5                     0     0              0            0
## 6                     0     0              0            0
##   mobile_likes_received www_likes www_likes_received
## 1                     0         0                  0
## 2                     0         0                  0
## 3                     0         0                  0
## 4                     0         0                  0
## 5                     0         0                  0
## 6                     0         0                  0
```

Friend counts histogram faceted by gender

```r
qplot(x = friend_count, data = subset(pseudo_facebook, !is.na(gender)), binwidth = 25
      , color = I('black'), fill = I('blue')) +
  xlab("Number of friends") +
  ylab("Number of users") +
  scale_x_continuous(limits = c(0, 1000), breaks = seq(0, 1000, 100)) +
  facet_wrap(~gender)
```

![](One_Variable_files/figure-html/unnamed-chunk-5-1.png)<!-- -->

Friend count statistics by gender

```r
by(pseudo_facebook$friend_count, pseudo_facebook$gender, summary)
```

```
## pseudo_facebook$gender: female
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
##       0      37      96     242     244    4923 
## -------------------------------------------------------- 
## pseudo_facebook$gender: male
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
##       0      27      74     165     182    4917
```


```r
qplot(x = (tenure/365), data = pseudo_facebook, binwidth = .25 
      , color = I('black'), fill = I('blue')) +
  xlab("Number of years using facebook") +
  ylab("Number of users") +
  scale_x_continuous(breaks = seq(1, 7, 1), limits = c(0, 7)) +
  ggtitle("Tenure histogram in years")
```

![](One_Variable_files/figure-html/unnamed-chunk-7-1.png)<!-- -->


```r
qplot(x=age, data=pseudo_facebook, binwidth=1
      , color = I('black'), fill = I('blue')) +
  xlab("User's age") +
  ylab("Number of users") +
  scale_x_continuous(breaks=seq(0, 150, 5)) +
  scale_y_continuous(breaks=seq(0, 6000, 500)) +
  ggtitle("User's age histogram")
```

![](One_Variable_files/figure-html/unnamed-chunk-8-1.png)<!-- -->

Three different friend's count histograms: 1) Count, 2) Base 10 log transformation of count, and 3) Square root transformation of count 

```r
fc <- qplot(x = friend_count, data = pseudo_facebook, binwidth=25
            , color = I('black'), fill = I('blue')) +
  xlab('Number of friends') +
  ylab('Number of users') +
  scale_x_continuous(breaks=seq(0, 5000, 100), limits=c(0, 2500))

fc_log10 <- qplot(x = friend_count+1, data = pseudo_facebook, binwidth=.05
                  , color = I('black'), fill = I('blue')) +
  xlab('Number of friends (Base 10 logarithm transformation)') +
  ylab('Number of users') +
  scale_x_log10(breaks=seq(0, 2500, 300), limits=c(1, 2500))

fc_sqrt <- qplot(x = friend_count, data = pseudo_facebook, binwidth=.75
                  , color = I('black'), fill = I('blue')) +
  xlab('Number of friends (Square root transformation)') +
  ylab('Number of users') +
  scale_x_sqrt(breaks=seq(0, 5000, 100), limits=c(0, 2500))

grid.arrange(fc, fc_log10, fc_sqrt, nrow = 3)
```

![](One_Variable_files/figure-html/unnamed-chunk-9-1.png)<!-- -->

Frequency polygons of number of likes by gender

```r
qplot(x = www_likes, y = ..count../sum(..count..), data = subset(pseudo_facebook, !is.na(gender))
      , geom = 'freqpoly', color = gender
      , xlab = 'Likes Count'
      , ylab = 'Number of Friends') + 
  scale_x_log10(breaks=seq(0,15000,1000), limits=c(1, 15000))
```

![](One_Variable_files/figure-html/unnamed-chunk-10-1.png)<!-- -->

What's the males like count and who has more likes, males or females?

```r
by(pseudo_facebook$www_likes, pseudo_facebook$gender, sum)
```

```
## pseudo_facebook$gender: female
## [1] 3507665
## -------------------------------------------------------- 
## pseudo_facebook$gender: male
## [1] 1430175
```

Who has more friends, males or females?
Boxplot of friend count by gender

```r
qplot(x = gender, y = friend_count,data = subset(pseudo_facebook, !is.na(gender))
      , geom = 'boxplot') +
  coord_cartesian(ylim=c(0, 1000))
```

![](One_Variable_files/figure-html/unnamed-chunk-12-1.png)<!-- -->

Numerical summary of friend count by gender

```r
by(pseudo_facebook$friend_count, pseudo_facebook$gender, summary)
```

```
## pseudo_facebook$gender: female
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
##       0      37      96     242     244    4923 
## -------------------------------------------------------- 
## pseudo_facebook$gender: male
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
##       0      27      74     165     182    4917
```

Who initiated more friendships, males or females?
Boxplot of friendships initiated by gender

```r
qplot(x = gender, y = friendships_initiated
      , data = subset(pseudo_facebook, !is.na(gender))
      , geom = 'boxplot') +
  coord_cartesian(ylim = c(0:150))
```

![](One_Variable_files/figure-html/unnamed-chunk-14-1.png)<!-- -->

Numerical summary of friendships initiated by gender

```r
by(pseudo_facebook$friendships_initiated, pseudo_facebook$gender, summary)
```

```
## pseudo_facebook$gender: female
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
##     0.0    19.0    49.0   113.9   124.8  3654.0 
## -------------------------------------------------------- 
## pseudo_facebook$gender: male
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
##     0.0    15.0    44.0   103.1   111.0  4144.0
```

Create new logical variable to identify if an user has ever logged in using a mobile device

```r
pseudo_facebook$mobile_check_in <- ifelse(pseudo_facebook$mobile_likes > 0, 1, 0)
pseudo_facebook$mobile_check_in <- factor(pseudo_facebook$mobile_check_in)
summary(pseudo_facebook$mobile_check_in)
```

```
##     0     1 
## 35056 63947
```
What percentage of users check in using mobile?

```r
sum(pseudo_facebook$mobile_check_in == 1)/length(pseudo_facebook$mobile_check_in)
```

```
## [1] 0.6459097
```
  





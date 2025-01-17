---
title: "Multivariate - Problem Set"
output: 
  html_document: 
    keep_md: yes
---

Import necessary libraries and data sets


```r
library(ggplot2)
data(diamonds)
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
pseudo_facebook$years_joined <- floor(2014 - (pseudo_facebook$tenure/365))
pseudo_facebook$years_joined.bucket <- cut(pseudo_facebook$years_joined, c(2004, 2009, 2011, 2012, 2014))
```

Histogram of diamond prices faceted by diamond color and colored by cut

```r
ggplot(data=diamonds, aes(x=log(price), color=cut)) + geom_histogram() + facet_wrap(~color)
```

![](Multivariate_-_Problem_Set_files/figure-html/unnamed-chunk-4-1.png)<!-- -->

Scatterplot of diamond price vs.table and color the points by the cut of the diamond  

```r
ggplot(data=diamonds, aes(x=table, y=price, color=cut)) + geom_point()
```

![](Multivariate_-_Problem_Set_files/figure-html/unnamed-chunk-5-1.png)<!-- -->

Scatterplot of diamond price vs. volume (x * y * z) and color the points by the clarity of diamonds. 
Use scale on the y-axis to take the log10 of price. You should also omit the top 1% of diamond volumes from the plot.

```r
diamonds$volume <- (diamonds$x*diamonds$y*diamonds$z)

ggplot(data=subset(diamonds, diamonds$volume < quantile(diamonds$volume, 0.99))
       , aes(x=volume, y=price, color=clarity)) + geom_point() + scale_y_log10()
```

![](Multivariate_-_Problem_Set_files/figure-html/unnamed-chunk-6-1.png)<!-- -->

New variable called 'prop_initiated' which contains the proportion of friendships that an user initiated

```r
pseudo_facebook$prop_initiated <- pseudo_facebook$friendships_initiated/pseudo_facebook$friend_count
```

Line graph of the median proportion of friendships initiated ('prop_initiated') vs. tenure and color the line segment by year_joined.bucket

```r
ggplot(data=pseudo_facebook, aes(x=tenure, y=prop_initiated)) + 
    geom_line(aes(color = years_joined.bucket), stat = 'summary', fun.y = median)
```

![](Multivariate_-_Problem_Set_files/figure-html/unnamed-chunk-8-1.png)<!-- -->

Smoothened prop_initiated vs tenure colored by year_joined.bucket. You can bin together ranges of tenure or add a smoother to the plot.

```r
ggplot(data=pseudo_facebook, aes(x=tenure, y=prop_initiated)) + 
    geom_smooth(aes(color = years_joined.bucket))
```

![](Multivariate_-_Problem_Set_files/figure-html/unnamed-chunk-9-1.png)<!-- -->

Scatter plot of the price/carat ratio of diamonds. Points colored by diamond color, and faceted by clarity  

```r
ggplot(data=diamonds, aes(x=cut, y=price/carat, color=color)) + geom_point() + facet_wrap(~clarity)
```

![](Multivariate_-_Problem_Set_files/figure-html/unnamed-chunk-10-1.png)<!-- -->


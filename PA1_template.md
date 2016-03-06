
# Coursera Data Science Track
### Reproducable Research, Project 1

## Source data preparation
Data used for this study may be found [here](https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip). Data source was provided as part of Reproducable Research Course and was awailable at _Sunday, March 6, 2016_.
Code below, checks if CSV data availaable in work directory and loads source into _activity_ data farme. Code all necessary steps to prepare raw data.


```r
  options(scipen=1,digits=6)
  # Source data  is not available in work directory.
  if (!file.exists("activity.csv")) {
    tfile <- tempfile()
    # Check platfoerm to handle HTTPs protocol properly
     dmethod <- switch(Sys.info()["sysname"], Linux="curl", Windows="wininet")
     download.file("https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip",
                tfile, quiet=T,method = dmethod);
    # Exctract activity.csv                
     unzip(tfile,overwrite = T)
    # cleanup preparation 
     unlink(tfile)
     rm(tfile,dmethod)
  }
  # Load Soruce data.
  
  activity<-read.csv("activity.csv")
```

Data set contans 3 variables, where variable date is a factor of characters.

```r
 str(activity)
```

```
## 'data.frame':	17568 obs. of  3 variables:
##  $ steps   : int  NA NA NA NA NA NA NA NA NA NA ...
##  $ date    : Factor w/ 61 levels "2012-10-01","2012-10-02",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ interval: int  0 5 10 15 20 25 30 35 40 45 ...
```

I've converted varaibe date to POSIXct format to enable proper timelaine analysis.

```r
 activity$date <- as.POSIXct(as.character(activity$date),"%Y-%m-%d")
```
## Eaxmine tidy data 
Compute daily activity using [dplyr](https://cran.rstudio.com/web/packages/dplyr/vignettes/introduction.html) library

```r
  require(dplyr)
  stepsbyday <- group_by(activity, date) %>% 
    summarize(steps=sum(steps,na.rm= TRUE))
```
Dayly based statistics gives us __9350__ as average amount of steps  with median equal to  __10400__. Othere summary facts are below:

```r
summary(stepsbyday$steps)
```

```
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
##       0    6780   10400    9350   12800   21200
```

## Impute missing mesurements



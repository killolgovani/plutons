```R
library(dbplyr)
library(dplyr)
library(odbc)
library(RPostgres)
library(plutoR)
options("scipen"=999)
source("config.R")

equitiesIndiaNse <- EquitiesIndiaNse()
indices<-Indices()
```

    
    Attaching package: ‘dplyr’
    
    The following objects are masked from ‘package:dbplyr’:
    
        ident, sql
    
    The following objects are masked from ‘package:stats’:
    
        filter, lag
    
    The following objects are masked from ‘package:base’:
    
        intersect, setdiff, setequal, union
    



```R
maxDt <- (equitiesIndiaNse$EodTimeSeries() %>%
  summarize(MAX_DT = max(TIME_STAMP)) %>%
  collect())$MAX_DT[[1]]

print("latest end-of-day prices for RELIANCE:")
equitiesIndiaNse$EodTimeSeries() %>%
  filter(TIME_STAMP == maxDt & SYMBOL == 'RELIANCE') %>%
  print()
```

    Warning message:
    “Missing values are always removed in SQL.
    Use `MAX(x, na.rm = TRUE)` to silence this warning
    This warning is displayed only once per session.”

    [1] "latest end-of-day prices for RELIANCE:"
    [90m# Source:   lazy query [?? x 9][39m
    [90m# Database: Microsoft SQL Server 13.00.4259[ro1@NORWAY/StockViz][39m
      SYMBOL   SERIES TIME_STAMP  HIGH   LOW  OPEN CLOSE  LAST   VOLUME
      [3m[90m<chr>[39m[23m    [3m[90m<chr>[39m[23m  [3m[90m<date>[39m[23m     [3m[90m<dbl>[39m[23m [3m[90m<dbl>[39m[23m [3m[90m<dbl>[39m[23m [3m[90m<dbl>[39m[23m [3m[90m<dbl>[39m[23m    [3m[90m<dbl>[39m[23m
    [90m1[39m RELIANCE EQ     2019-08-30 [4m1[24m254.  [4m1[24m221 [4m1[24m246. [4m1[24m249.  [4m1[24m253 11[4m3[24m[4m0[24m[4m8[24m120



```R
auto_index <- indices$NseTimeSeries() %>%
                 filter(NAME == "NIFTY 50") %>%  
                 arrange(desc(TIME_STAMP)) %>%
                 print(n=5)
```

    [90m# Source:     lazy query [?? x 7][39m
    [90m# Database:   Microsoft SQL Server 13.00.4259[ro1@NORWAY/StockViz][39m
    [90m# Ordered by: desc(TIME_STAMP)[39m
      NAME     TIME_STAMP   HIGH    LOW   OPEN  CLOSE    VOLUME
      [3m[90m<chr>[39m[23m    [3m[90m<date>[39m[23m      [3m[90m<dbl>[39m[23m  [3m[90m<dbl>[39m[23m  [3m[90m<dbl>[39m[23m  [3m[90m<dbl>[39m[23m     [3m[90m<dbl>[39m[23m
    [90m1[39m NIFTY 50 2019-08-30 [4m1[24m[4m1[24m043. [4m1[24m[4m0[24m875. [4m1[24m[4m0[24m988. [4m1[24m[4m1[24m023. 628[4m1[24m[4m5[24m[4m4[24m431
    [90m2[39m NIFTY 50 2019-08-29 [4m1[24m[4m1[24m021. [4m1[24m[4m0[24m922. [4m1[24m[4m0[24m996. [4m1[24m[4m0[24m948. 649[4m8[24m[4m7[24m[4m6[24m160
    [90m3[39m NIFTY 50 2019-08-28 [4m1[24m[4m1[24m130. [4m1[24m[4m0[24m988. [4m1[24m[4m1[24m101. [4m1[24m[4m1[24m046. 549[4m9[24m[4m5[24m[4m4[24m696
    [90m4[39m NIFTY 50 2019-08-27 [4m1[24m[4m1[24m142. [4m1[24m[4m1[24m050. [4m1[24m[4m1[24m107. [4m1[24m[4m1[24m105. 685[4m5[24m[4m5[24m[4m1[24m267
    [90m5[39m NIFTY 50 2019-08-26 [4m1[24m[4m1[24m070. [4m1[24m[4m0[24m757. [4m1[24m[4m1[24m000. [4m1[24m[4m1[24m058. 684[4m1[24m[4m4[24m[4m1[24m923
    [90m# … with more rows[39m



```R
auto <- as.data.frame(auto_index)
```


```R
returns <- diff(auto$CLOSE,lag=1)
```


```R
length(returns)
```


7054


This notebook was created using [pluto](http://pluto.studio). Learn more [here](https://github.com/shyams80/pluto)

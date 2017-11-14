


## Introduction

We conducted a two-stage multi-treatment or network meta-analysis for the simultaneous analysis of all treatments of interest and which co-occur in the same trial. This approach allows direct comparisons of treatments with each other, and takes into account all the correlations (see Madden et al. 2016 for more details). 

The most common form of effect size used in traditional meta-analysis are based on the contrasts of the treatment of interest with a common reference (e.g. control treatment), such as mean difference, response ratio, etc. This is known as the conditional modeling approach, also named contrast-based meta-analysis. An alternative and simpler approach, which is commonly used in plant pathology, is to fit a two-way linear mixed model directly to the treatment means. This is know as the unconditional modeling approach or arm-based meta-analysis. 


## Import data

Let's first import the data and load the packages needed for the analyses.


```r
library(readr)
fhb_sev <- read_csv("data/fhb_sev.csv")
```

```
## Warning: Missing column names filled in: 'X1' [1]
```

```
## Parsed with column specification:
## cols(
##   .default = col_integer(),
##   cultivar = col_character(),
##   reaction = col_character(),
##   location = col_character(),
##   state = col_character(),
##   AI = col_character(),
##   group = col_character(),
##   yield_check = col_double(),
##   sev_check = col_double(),
##   sev = col_double(),
##   V_sev = col_double(),
##   publication = col_character(),
##   mean_trial = col_double()
## )
```

```
## See spec(...) for full column specifications.
```

```r
fhb_yield <- read_csv("data/fhb_yield.csv")
```

```
## Warning: Missing column names filled in: 'X1' [1]
```

```
## Parsed with column specification:
## cols(
##   .default = col_integer(),
##   cultivar = col_character(),
##   reaction = col_character(),
##   location = col_character(),
##   state = col_character(),
##   AI = col_character(),
##   group = col_character(),
##   sev_check = col_double(),
##   yield_check = col_double(),
##   yield = col_double(),
##   V_yield = col_double(),
##   mean_yield = col_double(),
##   publication = col_character(),
##   author = col_character()
## )
## See spec(...) for full column specifications.
```



```r
# Load packages 
library(tidyverse)
library(metafor)
library(ggthemes)
library(cowplot)
library(scales)
library(knitr)
library(nloptr)
library(minqa)
library(DT)
```


## Effect-sizes

Here, we used the latter approach fitted directly to the log of the means (for both FHB index and yield) for further obtaining the relative effect (control efficacy and yield response). For yield, we fitted the model directly to the mean yield of treatments to further estimate the yield response, or the difference (D) from using the fungicides. The D was calculated for each treatment within a study for plotting purposes, but the meta-analyitic estimate of D was obtained from the difference of the estimates by mixed model fitted directly to the treatment means.



```r
# log of FHB index
fhb_sev$yi <- log(fhb_sev$sev)
hist(fhb_sev$yi)
```

![](code_files/figure-html/unnamed-chunk-3-1.png)<!-- -->

```r
# log of yield
fhb_yield$yi <- log(fhb_yield$yield)
hist(fhb_yield$yi)
```

![](code_files/figure-html/unnamed-chunk-3-2.png)<!-- -->

```r
# difference (D) in yield between treatment and non-treated check
fhb_yield$D <-fhb_yield$yield - fhb_yield$yield_check
hist(fhb_yield$D)
```

![](code_files/figure-html/unnamed-chunk-3-3.png)<!-- -->


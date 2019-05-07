# GAM smoothing vs GPS models
## Overview
There are two parts that are covered:
* Weekly average of PM<sub>10</sub> Measurement using GAM
* Understanding GPS data

## Part 1: PM<sub>10</sub> Measurement using GAM
According to the Datacamp course, a generalized additive model (GAM) is "a generalized linear model in which the linear predictor depends linearly on unknown smooth functions of some predictor variables, and interest focuses on inference about these smooth functions."

### 1. Personal rationale
It is worthwhile to use this approach because the smoothing functions of geo-environmental elements (e.g. DEM, XY coordinates, land use, buildings) might explain the complexity of pollution flow. In R CRAN, there are two packages called called **MAPGAM** and **mgcv**. MAPGAM (Mapping Smoothed Effect Estimates from Individual-Level Data) uses GAM for *mapping* odds ratios, other effect estimates using individual-level data such as case-control study data, while mgcv focuses on the mathematical perspective of GAM. After trying both, I decided to use mgcv for its miscellaneous applications e.g. convert to raster, analyse GIS files without pre-processing effort (MAPGAM does need some effort to bring shapefiles).

### 2. GAM Formula
Wiki says...
> The model relates a univariate response variable, Y, to some predictor variables, xi. An exponential family distribution is specified for Y (for example normal, binomial or Poisson distributions) along with a link function g (for example the identity or log functions) relating the expected value of Y to the predictor variables via a structure such as <br><br>
<a data-flickr-embed="true"  href="https://www.flickr.com/photos/139313330@N06/46291402691/in/dateposted-public/" title="gam"><img src="https://farm5.staticflickr.com/4823/46291402691_0e11701b66_z.jpg" width="567" height="32" alt="gam"></a> <br><br>
> The functions fi may be functions with a specified parametric form (for example a polynomial, or an un-penalized regression spline of a variable) or may be specified non-parametrically, or semi-parametrically, simply as 'smooth functions', to be estimated by non-parametric means. So a typical GAM might use a scatterplot smoothing function, such as a locally weighted mean, for f1(x1), and then use a factor model for f2(x2). This flexibility to allow non-parametric fits with relaxed assumptions on the actual relationship between response and predictor, provides the potential for better fits to data than purely parametric models, but arguably with some loss of interpretability.

https://en.wikipedia.org/wiki/Generalized_additive_model

### 3. Data preparation

Here is the weekly Average data shown in a data table. Click to enlarge image <br> 
<a data-flickr-embed="true"  href="https://www.flickr.com/photos/139313330@N06/46291336301/in/dateposted-public/" title="WeeklyAvPM10"><img src="https://farm5.staticflickr.com/4855/46291336301_36c9e8fb51_z.jpg" width="640" height="208" alt="WeeklyAvPM10"></a>
<br/><br/>

Note that `pm2013_XX` refers to: <br>
* `pm2013_30`: 2013 July week 4
* `pm2013_31`: 2013 August week 1
* `pm2013_32`: 2013 August week 2
* `pm2013_33`: 2013 August week 3
* `pm2013_34`: 2013 August week 4
* `pm2013_35`: 2013 September week 1
* `pm2013_36`: 2013 September week 2
* `pm2013_37`: 2013 September week 3
* `pm2013_38`: 2013 September week 4
* `pm2013_39`: 2013 September week 5

Here is a map of 46 stations considered in this study
<a data-flickr-embed="true"  href="https://www.flickr.com/photos/139313330@N06/32419692788/in/dateposted-public/" title="Seoul Pollution Station"><img src="https://farm5.staticflickr.com/4816/32419692788_74eab3dbb3_z.jpg" width="400" height="370" alt="Seoul Pollution Station"></a>

### 4. GAM calculation
I tested the GAM equation by using `gam()` function. The basic approach is to put the smoothing argument `s()` to the relevant variables. In my case, I put the `s(X,Y)` XY coordinates together as an interaction factor, and add a DEM smoother `s(DEM)`. <br>

The `"method = REML"` is a mathematical option that is used as default. From stackoverflow, REML is an acronym of REstricted Maximum Likelihood. Here is one of the online user's comments:
> > Indeed, the REML estimator of a variance component is usually (approximately) unbiased, while the ML estimator is negatively biased. However, the ML estimator usually has lower mean-squared error (MSE) than the REML estimator. So, if you want to be right on average, go with REML, but you pay for this with larger variability in the estimates. If you want to be closer to the true value on average, go with ML, but you pay for this with negative bias.

Since we have only 46 stations, I will leave the method as `REML`.
```r
jul.w4 <- gam(pm2013_30 ~ s(X, Y) + s(DEM), data = aq.pm10, method = "REML")
aug.w1 <- gam(pm2013_31 ~ s(X, Y) + s(DEM), data = aq.pm10, method = "REML")
aug.w2 <- gam(pm2013_32 ~ s(X, Y) + s(DEM), data = aq.pm10, method = "REML")
aug.w3 <- gam(pm2013_33 ~ s(X, Y) + s(DEM), data = aq.pm10, method = "REML")
aug.w4 <- gam(pm2013_34 ~ s(X, Y) + s(DEM), data = aq.pm10, method = "REML")
sep.w1 <- gam(pm2013_35 ~ s(X, Y) + s(DEM), data = aq.pm10, method = "REML")
sep.w2 <- gam(pm2013_36 ~ s(X, Y) + s(DEM), data = aq.pm10, method = "REML")
sep.w3 <- gam(pm2013_37 ~ s(X, Y) + s(DEM), data = aq.pm10, method = "REML")
sep.w4 <- gam(pm2013_38 ~ s(X, Y) + s(DEM), data = aq.pm10, method = "REML")
sep.w5 <- gam(pm2013_39 ~ s(X, Y) + s(DEM), data = aq.pm10, method = "REML")
```

Here is the R<sup>2</sup> of each model:
* July w4: .25
* August w1: .23
* August w2: .204
* August w3: .077
* August w4: .232
* September w1: .16
* September w2: .292
* September w3: .208
* September w4: .346
* September w5: .327

The default model used gaussian distribution for model fitting. However, we can change the options as mentioned below:
* `binomial(link = "logit")`
* `gaussian(link = "identity")`
* `Gamma(link = "inverse")`
* `inverse.gaussian(link = "1/mu^2")`
* `poisson(link = "log")`
* `quasi(link = "identity", variance = "constant")`
* `quasibinomial(link = "logit")`
* `quasipoisson(link = "log")`

All options need further investigation, but so far, `inverse.gaussian(link = "1/mu^2")` got the highest R<sup>2</sup> value. For example, September w5 was increased to .392.


### 5. GAM on a spatial grid
Now we got all the GAM models ready. But how will the model be translated to a map? The answer is to build a grid. As a start, I used 200 x 200 x 200 grid area of X, Y, Z(DEM).

According to Simon Wood's book "Generalized Additive Model 2nd Edition 2017", he noted that the model containing more than one smoothing function(e.g. f(x), f(y)) are only estimable within the additive constant. I will come back to that later, but so far, adding DEM into the model increased the R<sup>2</sup>. Click to enlarge image 

<a data-flickr-embed="true"  href="https://www.flickr.com/photos/139313330@N06/46241500612/in/dateposted-public/" title="Rplot05"><img src="https://farm5.staticflickr.com/4876/46241500612_0c59c97c39_z.jpg" width="640" height="267" alt="Rplot05"></a>


## Part 2: Understanding GPS data
Here is an example of the GPS data. As can be seen below, there are 13 columns in total and all measurements are taken every minute.

|period|who|group|loc1|loc1_1|time|pm10|pm2_5|pm1|long|lat|X|Y|
|:----------:|:-------:|:--------:|:--------:|:-------:|:------:|:------:|:------:|:------:|:------:|:------:|:------:|:------|
|2	|1	|2	|1	|32	|2013-07-25 20|57	|104.6	|97	|89.7	|126.9822844	|37.31476048	|198429.6307	|423945.4081|
|2	|1	|2	|1	|32	|2013-07-25 20|48	|106.6	|83	|78.2	|126.9822908	|37.31488669	|198430.1956	|423959.4156|

Here is a brief code info. I removed data from the pilot study.
* Period
  * Pilot: 1
  * Summer: 2
  * Winter: 3
* who: researcher 1-5
* group: cluster 1-9
* loc1(location category) 
  * indoors(no movement): 1
  * outdoors(no movement): 2
  * movement: 3
* loc1_1(specific location)
  * Restaurant: 10
  * Coffee shop: 11
  * Bbq grill: 12
  * Bar: 13
  * Office: 14
  * Traditional market: 15
  * Superstore: 16
  * Department store: 17
  * Shopping complex: 18
  * Other shops(including convenience store): 19
  * Workplace: 20
  * Bank: 21
  * School: 22
  * Academy(educational): 23
  * Bookshop: 24
  * Rest centre for seniors: 25
  * Stroll: 26
  * Walking: 27
  * Bus: 28
  * Subway: 29
  * Taxi: 30
  * Personal vehicle: 31
  * At home: 32
  * Missing data: 999

### Plots and Stats
This GPS track plot is derived from one researcher who commutes from Suwon to Seoul (1.5 hour distance).
Click to enlarge image.<br>
<a data-flickr-embed="true"  href="https://www.flickr.com/photos/139313330@N06/45569159254/in/dateposted-public/" title="그림1"><img src="https://farm5.staticflickr.com/4902/45569159254_2d8acb57a9_z.jpg" width="640" height="240" alt="그림1"></a>


* July (count by minutes)
```
PM10  < 50 < 100 < 150 < 200 > 200
  0    147   113    40     0     0
  1    115    96    28     0     1
  2    120   102    18     0     0
  3    140    87    12     0     1
  4    122    60    58     0     0
  5    124    59    57     0     0
  6    121    92    27     0     0
  7    112    68    60     0     0
  8     88    89    63     0     0
  9     96   121    19     2     2
  10    85   148     5     0     0
  11    63   151     7     3     0
  12   116    90    20     1     0
  13    81    69     9     0     0
  14    26   126     6     0     0
  15    60   118     2     0     0
  16    69    67     0     0     0
  17   120    60     0     0     0
  18   155    37     1     0     0
  19   167    10     2     0     1
  20   140    66     3     0     0
  21   119   121    25     5     0
  22   136   117    47     0     0
  23   161   116    23     0     0
  ```
* August (count by minutes)
```
PM10  < 50 < 100 < 150 < 200 > 200
  0    134     4     8    31     3
  1    190     1     0    15     0
  2    180     0     0     0     0
  3    197     1     0     0     1
  4    198     2     0    24    16
  5    180     0    39    20     1
  6    159     9    54    16     2
  7    136    25     6    62     0
  8    169    18    19    34     0
  9    124    64     0     0     0
  10   120    60     0     0     0
  11   110    63     1     0     0
  12    81    95     0     0     1
  13   156    47     4     0     0
  14   176    42     4     1     1
  15   236     4     0     0     0
  16   221    17     1     1     0
  17   222    13     2     1     0
  18   236     4     0     0     0
  19   165    33    10     1    12
  20   159    18     2     1     0
  21   141     9    16    24    19
  22   120     0     6    10    44
  23   120     0     9    18    33
```
* September (count by minutes)
```
PM10  < 50 < 100 < 150 < 200 > 200
  0    134     4     8    31     3
  1    190     1     0    15     0
  2    180     0     0     0     0
  3    197     1     0     0     1
  4    198     2     0    24    16
  5    180     0    39    20     1
  6    159     9    54    16     2
  7    136    25     6    62     0
  8    169    18    19    34     0
  9    124    64     0     0     0
  10   120    60     0     0     0
  11   110    63     1     0     0
  12    81    95     0     0     1
  13   156    47     4     0     0
  14   176    42     4     1     1
  15   236     4     0     0     0
  16   221    17     1     1     0
  17   222    13     2     1     0
  18   236     4     0     0     0
  19   165    33    10     1    12
  20   159    18     2     1     0
  21   141     9    16    24    19
  22   120     0     6    10    44
  23   120     0     9    18    33
```

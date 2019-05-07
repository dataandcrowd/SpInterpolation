GAM Update Apr.30th
================

## Brief Intro

I conducted a comparative analysis on two spatial interpolation methods,
Universal kriging (UK) and generalised additive model (GAM), to observe
any featured differences on the models and maps. I used hourly
pollutants of NO<sub>2</sub>, PM<sub>10</sub>, and O<sub>3</sub> that
were originally collected from 25 background and 15 roadside stations in
Seoul, as well as some from surrounding areas to minimise boundary
errors. Hourly data from each station were aggregated into a 12 hour
mean with names given as home hours and office hours. X, Y, and
elevation were used as predictors in both models. In terms of
implementing multiple space-time kriging models, I automated the
semi-variogram selection and parameterisation process to ease the
hardship of daily variogram fitting.

## Kriging

### Outcome: February 11th-20th, 2014

Here are some examples from Kriging interpolation. You can see the
entire results through this
[link](https://github.com/mrsensible/GAM/tree/master/GAM_update_0430_files).

The kriged trends in mid-February show that the overall NO<sub>2</sub>
starts off with 36.7µg/m<sup>3</sup>(sd±6.4) but continues to rise until
it reaches 80.2µg/m<sup>3</sup>(sd±9.3) on the 17th, then decreases to
54.2 µg/m<sup>3</sup>(sd±4.6). It also shows small contours in some
plots - called bull’s eye effects’ which means that the semivariogram
couldn’t find a similar value thus instead created a local value of its
own, meaning *poor estimation*.

![Kriging Aug 2013](GAM_update_0430_files/no2_02_S2_kr.png)

The figure below shows kriging interpolated map with road:nonroad ratio
weight applied to roads. High pollution is seen either in urban highways
or southern outskirts of the city.

![Kriging Aug 2013](GAM_update_0430_files/no2_02_S2_kr_rd.png)

### RMSE Kriging

Will compare RMSE results with GAM’s
outcome

| Dates | DayNight | August\_2013 | September\_2013 | December\_2013 | January\_2014 | February\_2014 |
| ----: | :------- | -----------: | --------------: | -------------: | ------------: | -------------: |
|     1 | Day      |         2.97 |            3.53 |           0.00 |          2.26 |           3.54 |
|     1 | Night    |         2.87 |            6.27 |           0.00 |          2.86 |           5.47 |
|     2 | Day      |         6.15 |            4.87 |           5.09 |          4.54 |           5.52 |
|     2 | Night    |         5.32 |            7.45 |           5.07 |          2.19 |           6.44 |
|     3 | Day      |         1.25 |            2.95 |           2.58 |          2.20 |           3.77 |
|     3 | Night    |         2.21 |            6.31 |           4.77 |          2.13 |           2.59 |
|     4 | Day      |         1.32 |            3.41 |           6.56 |          7.03 |           3.33 |
|     4 | Night    |         0.70 |            5.76 |           5.38 |          6.51 |           2.01 |
|     5 | Day      |         1.06 |            4.91 |           4.57 |          7.10 |           6.06 |
|     5 | Night    |         3.12 |            3.08 |           4.23 |          7.21 |           3.65 |

NO2 RMSE table

<br><br>

## ~~GAM~~

### ~~Outcome~~

### ~~RMSE GAM~~

<br><br><br>

## Population

### 1. Small case study

![scaleup](GAM_update_0430_files/how-to-scale-up.png)

  - 5 Students in total
  - Ignore travel modes
  - Select a representative point
  - Create a 1km ring buffer
  - Measure 12 hour mean/max values inside the buffer

<br>
### 2. Census 

To model the population-weighted exposure, two types of population - census, mobile LTE signal - will be tested.

![census](GAM_update_0430_files/pop2010.png)

![landcover](GAM_update_0430_files/seoul_mid-class_EN.png)

<br><br> \#\#\# ~~3. Mobile LTE signal~~

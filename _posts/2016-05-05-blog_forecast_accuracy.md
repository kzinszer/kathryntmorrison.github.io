---
title: "Measures of forecast accuracy"
author: "Kathryn"
date: "May 5, 2016"
output: html_document
layout: post
tags: [R, forecasting]
permalink: forecastaccuracy
comments: yes
---

Test 
There are a number of ways to summarize forecast accuracy across a forecast horizon, but I've come to realize recently how unclear it is which measure to use in which situation - and how non-standardized it is. 

Sometimes we have data with different units, sometimes we have data with the same units but hugely different scales, sometimes outliers, and so on. We often want a measure that isn't hugely sensitive to outliers and is scale-independent (relative). But, if we have data with many zeros, many relative measures can be problematic. 

<!--more-->

There seem to really be two primary types of forecast accuracy metrics:

1.  Comparison between the true observed value $$y_t$$ and the predicted value $$\hat{y_t}$$, based on the error e.g., $$e_t = y_t - \hat{y_t}$$

2.  Comparison between the accuracy of a proposed method ($$e_t$$ from above) and a baseline method, often a naive forecast such as the last available data point (random walk), or possibly adjusting for seasonal trends only.


The second option provides more flexibility but also potentially complicates comparisons between multiple proposed models, and is less interpretable than say a percentage error. 


The most commonly used metrics that fit into type (1) above are: <br>

| Measure                                                     | Formula (for Mean)                                        |
|-------------------------------------------------------------|-----------------------------------------------------------|
| Mean (Median) absolute error, M(d)AE                        | $$\frac{1}{n}\sum \| e_t\| $$                              |
| Mean (Median) squared error, M(d)SE                         | $$\frac{1}{n} \sum e_t^2$$                                |
| Root Mean (Median) squared error, M(d)SE                    | $$\sqrt{ \frac{1}{n} \sum e_t^2}$$                        |
| Mean (Median) absolute percentage error, M(d)APE            | $$\frac{1}{n} \sum \| \frac{e_t} {y_t}\|$$                  |
| Symmetric Mean (Median) absolute percentage error, SM(d)APE | $$\frac{1}{n} \sum \frac{\| e_t\|} { \|y_t\| + \|\hat{y_t}\|}$$ |



(I excluded the metrics based purely on ranking). <br>
<br>
<br>


| Measure  | Robustness to outliers | Ability to address zeros     | Comparisons across different scales |
|----------|------------------------|------------------------------|-------------------------------------|
| M(d)AE   | Stable                 | Defined $$y_t \in R$$        | Scale/unit dependent                |
| M(d)SE   | Sensitive              | Defined $$y_t \in R$$        | Scale/unit dependent                |
| RM(d)SE  | Sensitive              | Defined $$y_t \in R$$        | Scale/unit dependent                |
| M(d)APE  | Stable                 | Undefined if $$y = 0$$       | Relative measure (%)                |
| SM(d)APE | Stable                 | Undefined if $$y = \hat{y}$$ | Relative measure (%)                |

<br>

It's both interesting and frustrating that there are no standard best practices. In fact, inappropriate metrics have been frequently used even in large prediction competitions!$$^1$$. 


In my dissertation, I have Poisson count data at different scales, sometimes different units, with outliers, and with zero-inflation. I am struggling to find the best metric! I will likely end up using a ratio of mean absolute error between two methods since I am comparing (i) multivariate models to univariate, and (ii) temporal models to spatio-temporal. Ideally though, I would prefer to find a more interpretable measure. I am wondering if I can standardize something similar to MAPE, but using an average across the forecast horizon as the denominator, rather than the original value. I imagine something like this: 


Horizon-standardized MAPE $$= \frac{1}{n} \sum_{i=1}^n \frac{\|y_i - \hat{y_i}\| }{ \frac{1}{n} \sum_{i=1}^n y_i } $$ <br>


This denominator would not be zero except in degenerate cases, would be consistent across different models (whereas the window may change), and this would standardize each absolute measure of accuracy by the expected value across the horizon. This definitely has some interpretation limitations but I'm going to continue to think about it and maybe look at some really simple simulation studies. 

To be continued...

<br>
<br>
<br>


------------------------------------------------------------------------

$$^1$$ Hyndman, Rob J., and Anne B. Koehler. "Another look at measures of forecast accuracy." *International Journal of Forecasting* 22.4 (2006): 679-688.

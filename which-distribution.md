---
title: 'which-distribution'
teaching: 10
exercises: 2
---

:::::::::::::::::::::::::::::::::::::: questions 

- How do you write a lesson using R Markdown and `{sandpaper}`?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

- Explain how to use markdown with the new lesson template
- Demonstrate how to include pieces of code, figures, and nested challenge blocks

::::::::::::::::::::::::::::::::::::::::::::::::

## Introduction

Certain statistical tests and procedures requires that our data
is normally distributed. And we have to use other tests if it is
not normally distributed. In that case we need to figure out which
statistical distribution best fit our data.

We could find that out by guessing at a distribution, fit our data
to it, and evaluate how good the fit is.

Or we can figure out a way to test at lot of different distributions
in one go.

The package `gamlss` and its plugin packages `gamlss.dist` and `gamlss.add`
can do just that:



``` r
library(tidyverse)
library(gamlss)
library(gamlss.dist)
library(gamlss.add)
```

`gamlss` has the advantage of implementing a _lot_ of different statistical 
distributions.

Let us look at some data. The `faithful` data set contains 272 observations of
the Old Faithful geyser in Yellowstone National Park in USA. We only look at 
eruptions that lasts longer than 3 seconds:



``` r
faithful %>% 
  filter(eruptions > 3) %>% 
  ggplot(aes(eruptions)) +
  geom_histogram(bins=20)
```

<img src="fig/which-distribution-rendered-unnamed-chunk-1-1.png" style="display: block; margin: auto;" />
This does not look very normally distributed, and the fact that eruptions can not
have negative lengths in it self, indicates that the data is probably not normally
distributed. This is because the normal distribution would give us non-zero 
probabilities for negative eruption-lengths.

So - which statistical distribution best matches our data?

We begin by preparing the data.:


``` r
eruption_example <- faithful %>% 
  filter(eruptions > 3) %>% 
  select(eruptions)
```

The function `fitDist()` from `gamlss` will fit the data to a selection of different
statistical distributions, calculate a measure of the goodness of fit, and return
the best fit (and information on all the others). Rather than testing against all 
97 different distributions supported by `gamlss`, we can specify only a selection,
in this case `realplus`, that only includes the 23 distributions that are defined
for positive, real numbers:



``` r
fit <- fitDist(eruptions, type = "realplus", data = eruption_example)
```

``` output
  |                                                                              |                                                                      |   0%  |                                                                              |===                                                                   |   4%  |                                                                              |======                                                                |   9%  |                                                                              |=========                                                             |  13%  |                                                                              |============                                                          |  17%  |                                                                              |===============                                                       |  22%  |                                                                              |==================                                                    |  26%  |                                                                              |=====================                                                 |  30%  |                                                                              |========================                                              |  35%  |                                                                              |===========================                                           |  39%  |                                                                              |==============================                                        |  43%  |                                                                              |=================================                                     |  48%  |                                                                              |=====================================                                 |  52%  |                                                                              |========================================                              |  57%  |                                                                              |===========================================                           |  61%  |                                                                              |==============================================                        |  65%  |                                                                              |=================================================                     |  70%  |                                                                              |====================================================                  |  74%  |                                                                              |=======================================================               |  78%  |                                                                              |==========================================================            |  83%Error in solve.default(oout$hessian) : 
  Lapack routine dgesv: system is exactly singular: U[4,4] = 0
  |                                                                              |=============================================================         |  87%Error in solve.default(oout$hessian) : 
  Lapack routine dgesv: system is exactly singular: U[4,4] = 0
  |                                                                              |================================================================      |  91%  |                                                                              |===================================================================   |  96%  |                                                                              |======================================================================| 100%
```
If you do this yourself, you will notice a lot of error-messages. It is not 
possible to fit this particular data to _all_ the distributions, and the ones
where the fit fails (enough), we will get an error message.

The output from `fitDist()` will return the best fit:


``` r
fit
```

``` output

Family:  c("WEI2", "Weibull type 2") 
Fitting method: "nlminb" 

Call:  gamlssML(formula = y, family = DIST[i]) 

Mu Coefficients:
[1]  -18.69
Sigma Coefficients:
[1]  2.524

 Degrees of Freedom for the fit: 2 Residual Deg. of Freedom   173 
Global Deviance:     175.245 
            AIC:     179.245 
            SBC:     185.574 
```
We are told that the statistical distribution that best fits the data is
`Weibull type 2` and that the AIC-measurement of goodness of fit is 170.245.

:::: callout
Is that a good fit? That is a good question. It strongly depends on the 
values in the dataset. In this dataset, the length of the eruptions are 
measured in seconds. If we choose to measure that length in another unit, eg
`wiblies` defined as two seconds - equivalent to dividing the values with 2,
the distribution should not change. But the AIC will change.

We can use the AIC to decide that one distribution fits the data better than 
another, but not to conclude that that distribution is the correct one.
::::

The `fit` object containing the output of the `fitDist()` function contains 
quite a bit more.

If we start by getting the errors out of the way, `fit$failed` returns the 
two distributions that failed enough to cause errors:


``` r
fit$failed
```

``` output
[[1]]
[1] "GIG"

[[2]]
[1] "LNO"
```
As mentioned `fitDist()` fitted the data to 23 different distributions. We can
inspect the rest, and their associated AIC-values like this:


``` r
fit$fits
```

``` output
    WEI2     WEI3      WEI       GG    BCPEo     BCPE    BCCGo     BCCG 
179.2449 179.2449 179.2449 181.1349 181.4953 181.4953 183.1245 183.1245 
     GB2      BCT     BCTo   exGAUS       GA   LOGNO2    LOGNO       IG 
183.1354 185.1245 185.1245 190.2994 194.4665 198.3047 198.3047 198.3558 
  IGAMMA      EXP       GP PARETO2o  PARETO2 
202.6759 861.8066 863.8067 863.8079 863.8081 
```
Here we get `WEI2` first, with an AIC of 179.2449, but we can see that `WEI3` and
`WEI1` have almost exactly the same AIC. Not that surprising if we guess that 
`Weibull type 3` is probably rather similar to `Weibull type 2`. 

The difference in AIC for the first two distributions tested is very small. Is it
large enough for us to think that `WEI2` is significantly better than `WEI3`?

No. As a general rule of thumb, the difference between the AIC of two distributions
have to be larger than 2 for us to see a significant difference.

We can get more details using the `summary()` function:

``` r
summary(fit)
```

``` output
*******************************************************************
Family:  c("WEI2", "Weibull type 2") 

Call:  gamlssML(formula = y, family = DIST[i]) 

Fitting method: "nlminb" 


Coefficient(s):
             Estimate  Std. Error  t value   Pr(>|t|)    
eta.mu    -18.6934274   1.1306427 -16.5334 < 2.22e-16 ***
eta.sigma   2.5242093   0.0589965  42.7858 < 2.22e-16 ***
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

 Degrees of Freedom for the fit: 2 Residual Deg. of Freedom   173 
Global Deviance:     175.245 
            AIC:     179.245 
            SBC:     185.574 
```
And we can get at graphical description as well:


``` r
plot(fit)
```

<img src="fig/which-distribution-rendered-unnamed-chunk-8-1.png" style="display: block; margin: auto;" />

``` output
******************************************************************
	      Summary of the Quantile Residuals
                           mean   =  -0.001205749 
                       variance   =  0.9953007 
               coef. of skewness  =  0.09022876 
               coef. of kurtosis  =  2.529951 
Filliben correlation coefficient  =  0.9976953 
******************************************************************
```

## Hvordan med de andre muligheder?

Dem kan vi også få: 




``` r
fitte <- gamlss(eruptions ~ 1, family = LOGNO, data = test)
```

``` error
Error in eval(expr, envir, enclos): object 'test' not found
```

``` r
plot(fitte)
```

``` error
Error in eval(expr, envir, enclos): object 'fitte' not found
```
Og det var så de fordelinger der er definerede for reelle, positive tal. Vi går efter den mindste "deviance" - afvigelsen fra idealet. Bemærk at jo flere datapunkter vi har, jo større bliver tallene. Det bedste fit er dog stadig det med den laveste værdi.

"realline" dækker kontinuerte fordelingsfunktioner på hele den reelle tallinie

"realplus" dækker kontinuerte fordelingsfunktioner på den positive, reelle tallinie

"realAll" har alle kontinuerte fordelingsfunktioner på hele den relle tallinie, det vil sige alt der er omfattet af "realline" og "realplus"

"real0to1" de kontinuerte fordelingsfunktioner der er defineret for værdier mellem 0 og 1.

"counts" fordelingsfunktioner for tælletal

"binom" fordelingsfunktioner for binomiale data.

Der er mange. Læs mere om dem i [dokumentationen](https://www.gamlss.com/wp-content/uploads/2013/01/gamlss-manual.pdf), særligt appendix A.
   
## Hvordan med de andre fordelinger?

Inde bagved er `fitDist` i virkeligheden gentagne kald til den samme funktion.
Det afsløres i outputtet:
`Call:  gamlssML(formula = y, family = DIST[i])` 

Så vil vi gerne have estimaterne fra de andre funktioner, kan vi få dem ved
at lave samme kald - men med en anden `family`:

``` r
nyt_fit <- gamlssML(formula = y, family = "GG")
```

``` error
Error in eval(predvars, data, env): object 'y' not found
```

``` r
LOGNO2    
```

``` output
function (mu.link ="log", sigma.link="log") 
{
    mstats <- checklink("mu.link", "LOGNO2", substitute(mu.link), c("inverse", "log", "identity"))
    dstats <- checklink("sigma.link", "LOGNO2", substitute(sigma.link), c("inverse", "log", "identity"))        
    structure(
          list(family = c("LOGNO2", "Log Normal 2"),
           parameters = list(mu=TRUE,sigma=TRUE),
                nopar = 2, 
                 type = "Continuous",
              mu.link = as.character(substitute(mu.link)), 
           sigma.link = as.character(substitute(sigma.link)), 
           mu.linkfun = mstats$linkfun, 
        sigma.linkfun = dstats$linkfun, 
           mu.linkinv = mstats$linkinv, 
        sigma.linkinv = dstats$linkinv,
                mu.dr = mstats$mu.eta, 
             sigma.dr = dstats$mu.eta, 
                 dldm = function(y, mu, sigma){
                 	      dldm <- (1/sigma^2)*(log(y)-log(mu))/mu #(log(y)-log(mu))/((sigma^2)*mu) 
                          dldm
                           },
               d2ldm2 = function(mu, sigma) -1/((sigma^2)*(mu^2)),
                 dldd = function(y,mu,sigma) {
                 	           dldd <- (1/(sigma^3))*((log(y)-log(mu))^2-sigma^2)
                              dldd}, 
               d2ldd2 = function(sigma) -(2/(sigma^2)),
              d2ldmdd = function(y) rep(0,length(y)),
          G.dev.incr  = function(y,mu,sigma,...)  -2*dLOGNO2(y,mu,sigma,log=TRUE),                         
                rqres = expression(rqres(pfun="pLOGNO2", type="Continuous", y=y, mu=mu, sigma=sigma)),
           mu.initial = expression({   mu <- exp((log(y)+mean(log(y)))/2 ) }),
        sigma.initial = expression({sigma <- rep(sd(log(y)),length(y))}), 
             mu.valid = function(mu) all (mu > 0) , 
          sigma.valid = function(sigma) all(sigma > 0), 
              y.valid = function(y)  all(y>0)
          ),
            class = c("gamlss.family","family")
          )
}
<bytecode: 0x55edc220c450>
<environment: namespace:gamlss.dist>
```

``` r
nyt_fit
```

``` error
Error in eval(expr, envir, enclos): object 'nyt_fit' not found
```
Resultatet kan behandles ganske som tidligere.


``` r
test <- as_tibble(faithful) %>% 
select(eruptions) %>% 
  filter(eruptions > 3)

faithful$eruptions
```

``` output
  [1] 3.600 1.800 3.333 2.283 4.533 2.883 4.700 3.600 1.950 4.350 1.833 3.917
 [13] 4.200 1.750 4.700 2.167 1.750 4.800 1.600 4.250 1.800 1.750 3.450 3.067
 [25] 4.533 3.600 1.967 4.083 3.850 4.433 4.300 4.467 3.367 4.033 3.833 2.017
 [37] 1.867 4.833 1.833 4.783 4.350 1.883 4.567 1.750 4.533 3.317 3.833 2.100
 [49] 4.633 2.000 4.800 4.716 1.833 4.833 1.733 4.883 3.717 1.667 4.567 4.317
 [61] 2.233 4.500 1.750 4.800 1.817 4.400 4.167 4.700 2.067 4.700 4.033 1.967
 [73] 4.500 4.000 1.983 5.067 2.017 4.567 3.883 3.600 4.133 4.333 4.100 2.633
 [85] 4.067 4.933 3.950 4.517 2.167 4.000 2.200 4.333 1.867 4.817 1.833 4.300
 [97] 4.667 3.750 1.867 4.900 2.483 4.367 2.100 4.500 4.050 1.867 4.700 1.783
[109] 4.850 3.683 4.733 2.300 4.900 4.417 1.700 4.633 2.317 4.600 1.817 4.417
[121] 2.617 4.067 4.250 1.967 4.600 3.767 1.917 4.500 2.267 4.650 1.867 4.167
[133] 2.800 4.333 1.833 4.383 1.883 4.933 2.033 3.733 4.233 2.233 4.533 4.817
[145] 4.333 1.983 4.633 2.017 5.100 1.800 5.033 4.000 2.400 4.600 3.567 4.000
[157] 4.500 4.083 1.800 3.967 2.200 4.150 2.000 3.833 3.500 4.583 2.367 5.000
[169] 1.933 4.617 1.917 2.083 4.583 3.333 4.167 4.333 4.500 2.417 4.000 4.167
[181] 1.883 4.583 4.250 3.767 2.033 4.433 4.083 1.833 4.417 2.183 4.800 1.833
[193] 4.800 4.100 3.966 4.233 3.500 4.366 2.250 4.667 2.100 4.350 4.133 1.867
[205] 4.600 1.783 4.367 3.850 1.933 4.500 2.383 4.700 1.867 3.833 3.417 4.233
[217] 2.400 4.800 2.000 4.150 1.867 4.267 1.750 4.483 4.000 4.117 4.083 4.267
[229] 3.917 4.550 4.083 2.417 4.183 2.217 4.450 1.883 1.850 4.283 3.950 2.333
[241] 4.150 2.350 4.933 2.900 4.583 3.833 2.083 4.367 2.133 4.350 2.200 4.450
[253] 3.567 4.500 4.150 3.817 3.917 4.450 2.000 4.283 4.767 4.533 1.850 4.250
[265] 1.983 2.250 4.750 4.117 2.150 4.417 1.817 4.467
```

``` r
gamlss(eruptions ~ 1, family = LOGNO2, data = eruption_example) %>% plot()
```

``` output
GAMLSS-RS iteration 1: Global Deviance = 194.3047 
GAMLSS-RS iteration 2: Global Deviance = 194.3047 
```

<img src="fig/which-distribution-rendered-unnamed-chunk-11-1.png" style="display: block; margin: auto;" />

``` output
******************************************************************
	      Summary of the Quantile Residuals
                           mean   =  -2.44886e-16 
                       variance   =  1.005747 
               coef. of skewness  =  -0.7063289 
               coef. of kurtosis  =  3.179548 
Filliben correlation coefficient  =  0.9814924 
******************************************************************
```


::::::::::::::::::::::::::::::::::::: keypoints 

- The data generating function is not neccesarily the same as the distribution that best fit the data.


::::::::::::::::::::::::::::::::::::::::::::::::


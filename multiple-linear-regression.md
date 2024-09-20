---
title: 'Multiple Linear Regression'
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

A linear regression is relatively simple to understand and visualise. 
We have a dependent variable that we try to predict - model - using an 
independent variable. In principle we can change the independent variable, and 
the model will predict the change in the dependent variable. We can visualise
this using a relatively simple graph, plotting the two variables against each other,
and adding the line representing the model.

But what if we have reason to believe that the dependent variable depends on
more than one independent variable?

Let us find some data for illustrating this.

Before anything else, we load the `tidyverse` package, and then load the
data:


``` r
library(tidyverse)
```

``` output
── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
✔ dplyr     1.1.4     ✔ readr     2.1.5
✔ forcats   1.0.0     ✔ stringr   1.5.1
✔ ggplot2   3.5.1     ✔ tibble    3.2.1
✔ lubridate 1.9.3     ✔ tidyr     1.3.1
✔ purrr     1.0.2     
── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
✖ dplyr::filter() masks stats::filter()
✖ dplyr::lag()    masks stats::lag()
ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors
```

``` r
density <- read_csv("data/BONEDEN.csv")
```

``` output
Rows: 41 Columns: 25
── Column specification ────────────────────────────────────────────────────────
Delimiter: ","
dbl (25): ID, age, zyg, ht1, wt1, tea1, cof1, alc1, cur1, men1, pyr1, ls1, f...

ℹ Use `spec()` to retrieve the full column specification for this data.
ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

The data records the bonedensity in different parts of the skeleton of 
female monozygotic twins, as well as height, age, weight, smoking, alcohol, tea and 
coffee-habits. We are going to observe the second twin, where all variables are
suffixed with the digit "2":


``` r
density <- density %>% select(age, contains("2"))
head(density)
```

``` output
# A tibble: 6 × 12
    age   ht2   wt2  tea2  cof2  alc2  cur2  men2  pyr2   ls2   fn2   fs2
  <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl>
1    27   160    56    42    21     0     0     0  13.8  0.76  0.68  1.04
2    42   159    72    20    21     1     1     0  48    0.89  0.64  1.11
3    59   156    54     7    28     0     0     1  20.5  0.51  0.64  0.86
4    61   162    58    21    35     0     0     1  29.8  0.85  0.69  1.03
5    47   150    58    91     0     0     1     1  25    0.59  0.54  0.96
6    33   158    54    14    14     0     0     0   5    0.83  0.5   1.06
```

More specically we are trying to model the bone density in the femoral shaft (lårbensskaft). As an intial hypothesis, we are going to assume that the density
depends on height (`ht2`), age (`age`), weight (`wt2`), tea (`tea2`) and coffee
consumption (`cof2`).

That is, we assume that there is a linear correlation between the bone density
and each of the independent variables.

The simple linear regresion between just the bone density and the height, can be
done with:


``` r
lm(fs2 ~ ht2, data = density) %>% 
  summary()
```

``` output

Call:
lm(formula = fs2 ~ ht2, data = density)

Residuals:
     Min       1Q   Median       3Q      Max 
-0.41406 -0.02810  0.00190  0.07658  0.30722 

Coefficients:
            Estimate Std. Error t value Pr(>|t|)    
(Intercept) -1.78744    0.71928  -2.485 0.017351 *  
ht2          0.01766    0.00445   3.969 0.000301 ***
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

Residual standard error: 0.1315 on 39 degrees of freedom
Multiple R-squared:  0.2877,	Adjusted R-squared:  0.2694 
F-statistic: 15.75 on 1 and 39 DF,  p-value: 0.0003006
```
Where we pipe the output of the regression to the summary function to take a 
closer look. There is a strong correlation between bone density and height. If
we increse the height of a woman by one centimeter, the density of the femoral
shaft is expected to increase by 0.0177 $g/cm^3$. The correlation is significant,
we are rather sure that the estimate of this coefficient is different from 0.

Now we would like to add the other independent variables to the model.

The model we are building can be described using math as:

$$ \text{fs2} = w_0 + w_1\text{ht2} + w_2\text{wt2} + w_3\text{tea2} + w_4\text{cof2} + \epsilon  $$
That is - we would like to figure out which coefficients $w_1$ to $w_4$, and 
which intercept $w_0$ gives us estimates of fs2, that minimises the error $\epsilon$

This is pretty straight forward. Instead of running `lm(fs2~ht2, data = density`,
we add the additional independent variables using the `+` sign. And again we 
pipe the result to the summary function to take a closer look:


``` r
lm(fs2 ~ ht2 + age + wt2 + tea2 + cof2, data = density) %>% summary()
```

``` output

Call:
lm(formula = fs2 ~ ht2 + age + wt2 + tea2 + cof2, data = density)

Residuals:
      Min        1Q    Median        3Q       Max 
-0.247601 -0.061517  0.005634  0.067826  0.166474 

Coefficients:
              Estimate Std. Error t value Pr(>|t|)    
(Intercept) -0.2800114  0.7774507  -0.360 0.720887    
ht2          0.0090956  0.0046455   1.958 0.058246 .  
age         -0.0068373  0.0015669  -4.364 0.000108 ***
wt2          0.0038274  0.0014934   2.563 0.014836 *  
tea2        -0.0009246  0.0009837  -0.940 0.353677    
cof2        -0.0005310  0.0009328  -0.569 0.572778    
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

Residual standard error: 0.1043 on 35 degrees of freedom
Multiple R-squared:  0.5978,	Adjusted R-squared:  0.5403 
F-statistic:  10.4 on 5 and 35 DF,  p-value: 3.566e-06
```
It is _that_ simple. Interpreting the results on the other hand is a little bit
more complicated. 

The bone density, for a newborn woman (0 years old), that is 0 centimeters high,
weighing 0 kilogram, who does not drink either tea or coffee, is -0.28 $g/cm^3$ - 
which is obviously either impossible, or a discovery that would get you an 
invitation to Stockholm.

For each centimeter the woman grows, _if all other variables are held constant!_
the bone density is expected to increase by 0.0091 $g/cm^3$.

For each year the woman grows older, _if all other variables are held constant!_
the bone density is expected to decrease by 0.0068 $g/cm^3$.

For each kilogram the woman puts on weight, _if all other variables are held constant!_
the bone density is expected to increase by 0.0038 $g/cm^3$.

The coefficient for the height variable is not significant (if we have decided
on a 5% significanse level), and neither consumption of teo nor coffee has a 
significant influence on bone density. According to this model.

Is this a good model? No. The coefficients for Tea and coffee consumption 
are not significant, and should not be in the model. Let us remove them.

But let us remove them one by one. First coffee that has the worst p-value:


``` r
lm(fs2 ~ ht2 + age + wt2 + tea2, data = density) %>% summary()
```

``` output

Call:
lm(formula = fs2 ~ ht2 + age + wt2 + tea2, data = density)

Residuals:
     Min       1Q   Median       3Q      Max 
-0.26036 -0.06261  0.01682  0.06366  0.17393 

Coefficients:
              Estimate Std. Error t value Pr(>|t|)    
(Intercept) -0.2209640  0.7632342  -0.290   0.7739    
ht2          0.0085364  0.0044976   1.898   0.0657 .  
age         -0.0068645  0.0015514  -4.425 8.58e-05 ***
wt2          0.0041140  0.0013927   2.954   0.0055 ** 
tea2        -0.0007497  0.0009257  -0.810   0.4233    
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

Residual standard error: 0.1034 on 36 degrees of freedom
Multiple R-squared:  0.5941,	Adjusted R-squared:  0.5489 
F-statistic: 13.17 on 4 and 36 DF,  p-value: 1.048e-06
```
Note that not only is the coefficient for tea still not significant, it actually
gets "worse". 



``` r
lm(fs2 ~ ht2 + age + wt2 , data = density) %>% summary()
```

``` output

Call:
lm(formula = fs2 ~ ht2 + age + wt2, data = density)

Residuals:
      Min        1Q    Median        3Q       Max 
-0.273490 -0.058038  0.005241  0.067527  0.169512 

Coefficients:
             Estimate Std. Error t value Pr(>|t|)    
(Intercept) -0.569569   0.627355  -0.908  0.36981    
ht2          0.010524   0.003751   2.806  0.00796 ** 
age         -0.006489   0.001474  -4.404 8.74e-05 ***
wt2          0.004077   0.001385   2.943  0.00559 ** 
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

Residual standard error: 0.1029 on 37 degrees of freedom
Multiple R-squared:  0.5867,	Adjusted R-squared:  0.5531 
F-statistic:  17.5 on 3 and 37 DF,  p-value: 3.078e-07
```


::::::::::::::::::::::::::::::::::::: keypoints 

- Use `.md` files for episodes when you want static content
- Use `.Rmd` files for episodes when you need to generate output
- Run `sandpaper::check_lesson()` to identify any issues with your lesson
- Run `sandpaper::build_lesson()` to preview your lesson locally

::::::::::::::::::::::::::::::::::::::::::::::::


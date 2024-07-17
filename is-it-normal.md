---
title: 'Testing for normality'
teaching: 10
exercises: 2
editor_options: 
  markdown: 
    wrap: 72
---

::: questions
-   How do you write a lesson using R Markdown and `{sandpaper}`?
:::

::: objectives
-   Explain how to use markdown with the new lesson template
-   Demonstrate how to include pieces of code, figures, and nested
    challenge blocks
:::


``` r
library(tidyverse)
library(palmerpenguins)
```

## How to test if our data is normally distributed?

Common question - is my data normally distributed?

What does it mean that it is normally distributed? It means that the
distribution of our data has the same properties as the normal
distribution.

Let us get some data that we can test:


``` r
normal_test_data <- penguins %>% 
  filter(species == "Chinstrap") %>% 
  select(bill_depth_mm)
```

### Mean and median

One of the properties of the normal distribution is that the mean and
median of the data is equal. Let us look at the penguins:


``` r
summary(normal_test_data)
```

``` output
 bill_depth_mm  
 Min.   :16.40  
 1st Qu.:17.50  
 Median :18.45  
 Mean   :18.42  
 3rd Qu.:19.40  
 Max.   :20.80  
```

This is actually pretty close! But equality between median and mean is a
neccesary, not a sufficient condition.

What next. A histogram of the data should look normal. Let us take a
closer look at bill_depth_mm where mean and median are closest:


``` r
normal_test_data %>% 
  ggplot(aes(x=bill_depth_mm)) +
  geom_histogram()
```

``` output
`stat_bin()` using `bins = 30`. Pick better value with `binwidth`.
```

<img src="fig/is-it-normal-rendered-unnamed-chunk-3-1.png" style="display: block; margin: auto;" />

This does not really look like a normal distribution. The fact that mean
and median are almost identical was not a sufficient criterium for
normalcy.

The shape of a histogram is heavily dependent on the bins we choose. A
better way of visualizing often density-plots:


``` r
normal_test_data %>% 
  ggplot(aes(x=bill_depth_mm)) +
  geom_density()
```

<img src="fig/is-it-normal-rendered-unnamed-chunk-4-1.png" style="display: block; margin: auto;" />

We can think of this as a histogram with infinitely small bins.

This does look more normal - but it would be nice to be able to quantize
the degree of normalcy.

## Percentiels and QQ-plots as a test

The properties of the normal distribution informs us that 50% of the
observations in the data are smaller than the mean (and conversely 50%
are larger). We alson know that 50% of the observations should be in the
interquartile range. And what we also know is that 2.5% of the
observations (the 2.5 percentile) are smaller than the mean minus 1.96
times the standard deviation.

Actually we can calculate exactly which percentile a given observation
is in. And we we can calculate which value a given percentile should
have.

Comparing those gives us an indication of how well the data conforms to
a normal distribution.

Rather than doing that by hand, we can get R to do it for us in a nice
graphical way:


``` r
normal_test_data %>% 
  ggplot(aes(sample = bill_depth_mm)) +
  geom_qq() +
  geom_qq_line()
```

<img src="fig/is-it-normal-rendered-unnamed-chunk-5-1.png" style="display: block; margin: auto;" />

The `geom_qq` function calculate and plots which percentile an
observation is in.

Rather than being given percentiles, we are given the value that the
percentile corresponds to if we calculate it as number of standard
deviations from the mean.

This results in plots that are more comparable.

`geom_qq_line` plots the line corresponding til the values the
percentiles should have, if the data was normally distributed.

A common theme is that the midle of the data falls relatively close to
the line, and that there are deviations from the line at both ends. In
this case the deviations are largish, indicating that the data is not
normally distributed.

::: callout
We have two tails in the qq-plot, a left and a right. And they can be
either above or below the qq-line.

That results in four different cases, that informs us about what is
wrong with the data - in regards to how it deviates from normalcy.

| Left tail | Right tail | Name         | What                                                                        |
|-----------|-----------|-----------|----------------------------------------|
| Above     | Below      | Leptokurtic  | Heavier tails - ie. more extreme values. Higher kurtosis                                    |
| Below     | Above      | Platykurtic  | Lighter tails - ie. fewer extreme values. Lower kurtosis                                   |
| Above     | Above      | Right skewed | A tail that stretches to the higher values - the extreme values are larger. |
| Below     | Below      | Left skewed  | A tail that stretches to the lower values - the extreme values are smaller. |


``` output

Attaching package: 'gridExtra'
```

``` output
The following object is masked from 'package:dplyr':

    combine
```

<img src="fig/is-it-normal-rendered-unnamed-chunk-6-1.png" style="display: block; margin: auto;" />
:::



Ja. kurtosis og skewness. Jo tættere skewness kommer på 0, og kurtosis
minus 3 på 0, jo tættere på normalfordelt er data.

R i sig selv kan ikke beregne det. Men det kan pakken e1071


``` r
library(e1071)
test <- rnorm(1000)
```

Skewness:


``` r
skewness(test)
```

``` output
[1] 0.02857772
```

kurtosis


``` r
kurtosis(test)
```

``` output
[1] -0.02368209
```

Bemærk at vores test-vektor er ret normal fordelt. Men ingen af
parametrene er lig 0.

Andre tests: Shapiro-Wilk


``` r
shapiro.test(test) 
```

``` output

	Shapiro-Wilk normality test

data:  test
W = 0.99773, p-value = 0.1877
```

nul-hypotesen er her at data er normalfordelte. Hvis vi afviser
null-hypotesen,vil det i dette tilfælde, være forkert i ca. 94% af
tilfældene.

Testen er særligt godt til små stikprøver (\<50, nogen siger den er ok
op op til \<2000)

Det er, vist nok, principielt et mål for den lineære korrelation mellem
data og normalfordelte kvantiler - altså det vi ser i qq-plottet.

### Kolmogorov-Smirnov

vi skal specificere at det er normalfordelingen vi tester imod
("pnorm") - den kan nemlig teste for andre fordelinger også.


``` r
ks.test(test, "pnorm", mean = mean(test), sd = sd(test))
```

``` output

	Asymptotic one-sample Kolmogorov-Smirnov test

data:  test
D = 0.026656, p-value = 0.4761
alternative hypothesis: two-sided
```

Vær forsigtig. Den forudsætter at vi kender "den sande" middelværdi og
standardafvigelse, i stedet for som i dette eksempel at estimere dem fra
vores stikprøve.

NULL-hypotesen er også her at data er normalfordelte, p-værdien er her
0.99, og vi kan derfor ikke afvise null-hypotesen.

### Liliefors test

Den er en variation af ks-testen, der er designet specifikt til at teste
normalitet. Og forudsætter *ikke* at vi på forhånd kender middelværdi og
standardafvigelse.


``` r
library(nortest)
lillie.test(test)
```

``` output

	Lilliefors (Kolmogorov-Smirnov) normality test

data:  test
D = 0.026656, p-value = 0.09102
```

Samme null-hypotese som før. Men læg igen mærke til at selvom data er
designet til at være normalfordelte, så er p-værdien ikke 1.

### Anderson-darling test

Ikke tilgængelig i R direkte:


``` r
library(nortest)
ad.test(test)
```

``` output

	Anderson-Darling normality test

data:  test
A = 0.64487, p-value = 0.09228
```

Også her er null-hypotesen at data er normaltfordelte.

::: keypoints
-   Use `.md` files for episodes when you want static content
-   Use `.Rmd` files for episodes when you need to generate output
-   Run `sandpaper::check_lesson()` to identify any issues with your
    lesson
-   Run `sandpaper::build_lesson()` to preview your lesson locally
:::

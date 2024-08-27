---
title: 'Table One'
teaching: 10
exercises: 2
---

:::::::::::::::::::::::::::::::::::::: questions 

- How do you make a Table One?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

- Explain what a Table One is
- Know how to make a Tabel One and adjust key parameters

::::::::::::::::::::::::::::::::::::::::::::::::

## What is a "Table One"?

Primarily used in medical and epidemiological research,
a Table One is typically the first table in any publication
using data.

It presents the baseline characteristics of the participants
in a study, and provides a concise overview of the relevant
demographic and clinical variables.

It typically compares different groups (male~female, treatment~control), to highlight similarities and differences.



``` output

Attaching package: 'table1'
```

``` output
The following objects are masked from 'package:base':

    units, units<-
```

It can look like this:
<!--html_preserve--><div class="Rtable1"><table class="Rtable1">
<thead>
<tr>
<th class='rowlabel firstrow lastrow'></th>
<th class='firstrow lastrow'><span class='stratlabel'>Treated<br><span class='stratn'>(N=20)</span></span></th>
<th class='firstrow lastrow'><span class='stratlabel'>Placebo<br><span class='stratn'>(N=20)</span></span></th>
<th class='firstrow lastrow'><span class='stratlabel'>Overall<br><span class='stratn'>(N=40)</span></span></th>
</tr>
</thead>
<tbody>
<tr>
<td class='rowlabel firstrow'>Sex</td>
<td class='firstrow'></td>
<td class='firstrow'></td>
<td class='firstrow'></td>
</tr>
<tr>
<td class='rowlabel'>Male</td>
<td>10 (50.0%)</td>
<td>10 (50.0%)</td>
<td>20 (50.0%)</td>
</tr>
<tr>
<td class='rowlabel lastrow'>Female</td>
<td class='lastrow'>10 (50.0%)</td>
<td class='lastrow'>10 (50.0%)</td>
<td class='lastrow'>20 (50.0%)</td>
</tr>
<tr>
<td class='rowlabel firstrow'>Age (years)</td>
<td class='firstrow'></td>
<td class='firstrow'></td>
<td class='firstrow'></td>
</tr>
<tr>
<td class='rowlabel'>Mean (SD)</td>
<td>29.4 (12.0)</td>
<td>31.0 (11.0)</td>
<td>30.2 (11.4)</td>
</tr>
<tr>
<td class='rowlabel'>Median [Min, Max]</td>
<td>23.4 [14.1, 49.0]</td>
<td>34.9 [10.0, 44.7]</td>
<td>32.2 [10.0, 49.0]</td>
</tr>
<tr>
<td class='rowlabel lastrow'>Missing</td>
<td class='lastrow'>1 (5.0%)</td>
<td class='lastrow'>0 (0%)</td>
<td class='lastrow'>1 (2.5%)</td>
</tr>
<tr>
<td class='rowlabel firstrow'>Weight (kg)</td>
<td class='firstrow'></td>
<td class='firstrow'></td>
<td class='firstrow'></td>
</tr>
<tr>
<td class='rowlabel'>Mean (SD)</td>
<td>69.7 (13.3)</td>
<td>70.8 (10.8)</td>
<td>70.3 (12.0)</td>
</tr>
<tr>
<td class='rowlabel lastrow'>Median [Min, Max]</td>
<td class='lastrow'>69.2 [45.2, 97.4]</td>
<td class='lastrow'>70.9 [49.6, 89.9]</td>
<td class='lastrow'>69.5 [45.2, 97.4]</td>
</tr>
</tbody>
</table>
</div><!--/html_preserve-->
We have 40 participants in a study, split into equal control and treatment groups,
and describe the distribution of Sex, Age and Weight in both the two groups, and overall.

A number of packages making it easy to make a Table One exists. Here we look
at the package `table1`.

The specific way of doing it depends on the data available. If we do not have
data on the weight of the participants, we are not able to describe the distribution
of their weight.

But the table above was made in this way:


``` r
library(table1)
table1(~sex + age + weight|treatment, data = dat)
```
We specify that we would like a table that is a function of `sex`, `age` and
`wt`, and we would like it split into groups based on the values in the `treatment`

It might be instructive to take a look at how the data was prepared.
First the data it self:

``` r
dat <- expand.grid(id=1:10, sex=c("Male", "Female"), treatment=c("Treated", "Placebo"))
dat$age <- runif(nrow(dat), 10, 50)
dat$age[3] <- NA  # Add a missing value
dat$weight <- exp(rnorm(nrow(dat), log(70), 0.2))
```

The interesting part occurs here, where we add labels to the data. That
allows us to control the headings in the table:

``` r
label(dat$sex) <- "Sex"
label(dat$age) <- "Age"
label(dat$treatment) <- "Treatment Group"
label(dat$weight) <- "Weight"
```

It can be beneficial to add information about units of the data:

``` r
units(dat$age) <- "years"
units(dat$weight) <- "kg"
```


## More advanced stuff

We might want to be able to precisely control the summary
statistics presented in the table. 

We can do that by specifying input to the arguments
`render.continuous` and `render.categorical` that
control how continuous and categorical data respecetively,
is shown in the table.

The simple way of doing that is by using abbrevieated
function names:


``` r
table1(~sex + age + weight|treatment, data = dat,
        render.continuous=c(.="Mean (SD%)", .="Median [Min, Max]",
                           "Geom. mean (Geo. SD%)"="GMEAN (GSD%)"))
```

<!--html_preserve--><div class="Rtable1"><table class="Rtable1">
<thead>
<tr>
<th class='rowlabel firstrow lastrow'></th>
<th class='firstrow lastrow'><span class='stratlabel'>Treated<br><span class='stratn'>(N=20)</span></span></th>
<th class='firstrow lastrow'><span class='stratlabel'>Placebo<br><span class='stratn'>(N=20)</span></span></th>
<th class='firstrow lastrow'><span class='stratlabel'>Overall<br><span class='stratn'>(N=40)</span></span></th>
</tr>
</thead>
<tbody>
<tr>
<td class='rowlabel firstrow'>Sex</td>
<td class='firstrow'></td>
<td class='firstrow'></td>
<td class='firstrow'></td>
</tr>
<tr>
<td class='rowlabel'>Male</td>
<td>10 (50.0%)</td>
<td>10 (50.0%)</td>
<td>20 (50.0%)</td>
</tr>
<tr>
<td class='rowlabel lastrow'>Female</td>
<td class='lastrow'>10 (50.0%)</td>
<td class='lastrow'>10 (50.0%)</td>
<td class='lastrow'>20 (50.0%)</td>
</tr>
<tr>
<td class='rowlabel firstrow'>Age (years)</td>
<td class='firstrow'></td>
<td class='firstrow'></td>
<td class='firstrow'></td>
</tr>
<tr>
<td class='rowlabel'>Mean (SD%)</td>
<td>29.4 (12.0%)</td>
<td>31.0 (11.0%)</td>
<td>30.2 (11.4%)</td>
</tr>
<tr>
<td class='rowlabel'>Median [Min, Max]</td>
<td>23.4 [14.1, 49.0]</td>
<td>34.9 [10.0, 44.7]</td>
<td>32.2 [10.0, 49.0]</td>
</tr>
<tr>
<td class='rowlabel'>Geom. mean (Geo. SD%)</td>
<td>27.1 (1.53%)</td>
<td>28.7 (1.55%)</td>
<td>27.9 (1.53%)</td>
</tr>
<tr>
<td class='rowlabel lastrow'>Missing</td>
<td class='lastrow'>1 (5.0%)</td>
<td class='lastrow'>0 (0%)</td>
<td class='lastrow'>1 (2.5%)</td>
</tr>
<tr>
<td class='rowlabel firstrow'>Weight (kg)</td>
<td class='firstrow'></td>
<td class='firstrow'></td>
<td class='firstrow'></td>
</tr>
<tr>
<td class='rowlabel'>Mean (SD%)</td>
<td>69.7 (13.3%)</td>
<td>70.8 (10.8%)</td>
<td>70.3 (12.0%)</td>
</tr>
<tr>
<td class='rowlabel'>Median [Min, Max]</td>
<td>69.2 [45.2, 97.4]</td>
<td>70.9 [49.6, 89.9]</td>
<td>69.5 [45.2, 97.4]</td>
</tr>
<tr>
<td class='rowlabel lastrow'>Geom. mean (Geo. SD%)</td>
<td class='lastrow'>68.5 (1.22%)</td>
<td class='lastrow'>70.0 (1.17%)</td>
<td class='lastrow'>69.3 (1.19%)</td>
</tr>
</tbody>
</table>
</div><!--/html_preserve-->
`table1` recognizes the following summary statisticis:
N, NMISS, MEAN, SD, CV, GMEAN, GCV, MEDIAN, MIN, MAX, IQR, 
Q1, Q2, Q3, T1, T2, FREQ, PCT

Details can be found in the help to the function `stats.default()` 

Note that they are case-insensitive, and we can write Median or mediAn instead
of median. 

Also note that we write `.="Mean (SD%)"` which will be recognized
as the functions `mean()` and `sd()`, but also that the label shown should be
"Mean (SD%)".

If we want to specify the label, we can write `"Geom. mean (Geo. SD%)"="GMEAN (GSD%)"`


:::: challenge
## Change the labels 

We have two unusual values in this table - geometric mean and geometric 
standard deviation. Change the code to write out "Geom." and "geo." as 
geometric.


:::: solution

``` r
table1(~sex + age + weight|treatment, data = dat,
        render.continuous=c(.="Mean (SD%)", .="Median [Min, Max]",
                           "Geometric mean (Geometric SD%)"="GMEAN (GSD%)"))
```

The geometric mean of two numbers is the squareroot of the product of the two
numbers. If we have three numbers, we take the cube root of the product. In general:

$$\left( \prod_{i=1}^{n} x_i \right)^{\frac{1}{n}}$$

The geometric standard deviation is defined by:
$$ \exp\left(\sqrt{\frac{1}{n} \sum_{i=1}^{n} \left( \log x_i - \frac{1}{n} \sum_{j=1}^{n} \log x_j \right)^2}\right)$$


::::

::::




## Very advanced stuff

If we want to specify the summary statistics very precisely, we have to 
define a function ourself:


``` r
my_summary <- function(x){
  c("","Median" = sprintf("%.3f", median(x, na.rm = TRUE)),
"Variance" = sprintf("%.1f", var(x, na.rm=TRUE)))
}
table1(~sex + age + weight|treatment, data = dat,
render.continuous = my_summary)
```

<!--html_preserve--><div class="Rtable1"><table class="Rtable1">
<thead>
<tr>
<th class='rowlabel firstrow lastrow'></th>
<th class='firstrow lastrow'><span class='stratlabel'>Treated<br><span class='stratn'>(N=20)</span></span></th>
<th class='firstrow lastrow'><span class='stratlabel'>Placebo<br><span class='stratn'>(N=20)</span></span></th>
<th class='firstrow lastrow'><span class='stratlabel'>Overall<br><span class='stratn'>(N=40)</span></span></th>
</tr>
</thead>
<tbody>
<tr>
<td class='rowlabel firstrow'>Sex</td>
<td class='firstrow'></td>
<td class='firstrow'></td>
<td class='firstrow'></td>
</tr>
<tr>
<td class='rowlabel'>Male</td>
<td>10 (50.0%)</td>
<td>10 (50.0%)</td>
<td>20 (50.0%)</td>
</tr>
<tr>
<td class='rowlabel lastrow'>Female</td>
<td class='lastrow'>10 (50.0%)</td>
<td class='lastrow'>10 (50.0%)</td>
<td class='lastrow'>20 (50.0%)</td>
</tr>
<tr>
<td class='rowlabel firstrow'>Age (years)</td>
<td class='firstrow'></td>
<td class='firstrow'></td>
<td class='firstrow'></td>
</tr>
<tr>
<td class='rowlabel'>Median</td>
<td>23.414</td>
<td>34.895</td>
<td>32.193</td>
</tr>
<tr>
<td class='rowlabel'>Variance</td>
<td>144.6</td>
<td>121.3</td>
<td>129.8</td>
</tr>
<tr>
<td class='rowlabel lastrow'>Missing</td>
<td class='lastrow'>1 (5.0%)</td>
<td class='lastrow'>0 (0%)</td>
<td class='lastrow'>1 (2.5%)</td>
</tr>
<tr>
<td class='rowlabel firstrow'>Weight (kg)</td>
<td class='firstrow'></td>
<td class='firstrow'></td>
<td class='firstrow'></td>
</tr>
<tr>
<td class='rowlabel'>Median</td>
<td>69.242</td>
<td>70.926</td>
<td>69.480</td>
</tr>
<tr>
<td class='rowlabel lastrow'>Variance</td>
<td class='lastrow'>177.7</td>
<td class='lastrow'>116.0</td>
<td class='lastrow'>143.3</td>
</tr>
</tbody>
</table>
</div><!--/html_preserve-->
We do not _need_ to use the `sprintf()` function, but it is a very neat way of 
combining text with numeric variables because it allows us to format 
them directly.

Summary statistics for categorical data can be adjusted similarly, by specifying
`render.categorical`.

:::: challenge
## What does %.3f actually do?

Can you guess what the formatting in ´sprintf´ does? 

Try to change "%.3f" in the function to "%.2f".

:::: solution


``` r
my_summary <- function(x){
  c("","Median" = sprintf("%.3f", median(x, na.rm = TRUE)),
"Variance" = sprintf("%.1f", var(x, na.rm=TRUE)))
}
```

`sprintf` uses a bit of an arcane way of specifying the way numbers should be
formatted when we combine them with text. The "%"-sign specifies that "this is
where we place the number in the function". ".3f" specifies that we are treating
the number as a floating point number (which is just a fancy way of saying that
it is a decimal number), and that we would like three digits after the decimal point.

::::



::::


:::: challenge

## Whats up with that blank line?

Note that in the function, we define a vector as output, with three elements:

``` r
my_summary <- function(x){
  c("",
"Median" = sprintf("%.3f", median(x, na.rm = TRUE)),
"Variance" = sprintf("%.1f", var(x, na.rm=TRUE)))
}
```

Calculating and formatting the median and the varianse is pretty straightforward.

But the first element is an empty string. Whats up with that?

:::: solution

Try to remove the empty string from the function, and use it is a table one 
as previously shown:


``` r
my_summary <- function(x){
  c("Median" = sprintf("%.3f", median(x, na.rm = TRUE)),
"Variance" = sprintf("%.1f", var(x, na.rm=TRUE)))
}
table1(~sex + age + weight|treatment, data = dat,
render.continuous = my_summary)
```
The line beginning with "Median" does not show up, but the median value is 
shown next to the "Age" and "Weight" lines. 

::::

::::





:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: instructor

Primarily of use if there are medical students on the course

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::


::::::::::::::::::::::::::::::::::::: keypoints 

- A Table One provides a compact describtion of the data we are working with
- With a little bit of work we can control the content of the table.

::::::::::::::::::::::::::::::::::::::::::::::::


---
title: 'Statistical tests'
teaching: 10
exercises: 2
---

:::::::::::::::::::::::::::::::::::::: questions 

- How do you write a lesson using R Markdown and `{sandpaper}`?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

- Explain how to use markdown with the new lesson template


::::::::::::::::::::::::::::::::::::::::::::::::

:::: instructor
Den her side er virkelig ikke egnet til undervisningsbrug. Det er oversigten
med eksempler og ret korte forklaringer.
Hele siden kan nok med fordel deles ret meget op.
::::

A collection of statistical tests

For all tests, the approach is:

1. Formulate hypotheses
2. Calculate test statistic
3. Determine p-value
4. Make decision

The cutoff chosen for all tests is a p-value of 0.05 unless otherwise indicated.



If a specific test is prefaced with "EJ KORREKTURLÆST" the text, examples etc
have not been checked.

## One sample tests


### One-sample chi-square test

::::spoiler
### Details

#### Used for

Testing whether observed categorical frequencies differ from expected 
frequencies under a specified distribution.

**Real-world example:** Mars Inc. claims a specific distribution of colours in
their M&M bags. Does the observed proportions in a given bag match their claim?


#### Assumptions

- Observations are independent.  
- Categories are mutually exclusive and collectively exhaustive.
- _Expected_ count in each category is at least 5 (for the chi-square approximation to be valid). The observed counts can be smaller.

#### Strengths
- Simple to compute and interpret.
- Does not require the data to be normally distributed.
- Applicable to any number of categories.

#### Weaknesses
- Sensitive to small expected counts.  
- Does not indicate which categories contribute most to the discrepancy without further investigation.
- Requires independence; cannot be used for paired or repeated measures.

#### Example

##### Hypothesis
- **Null hypothesis (H₀):** The proportions of M&M colours equal the manufacturer’s claimed distribution.  
- **Alternative hypothesis (H₁):** The proportion of at least one colour differs from the claimed distribution.



``` r
# Observed counts of M&M colors:
observed <- c(red = 20, blue = 25, green = 15, brown = 18, orange = 12, yellow = 10)

# Manufacturer's claimed proportions:
p_expected <- c(red = 0.20, blue = 0.20, green = 0.20, brown = 0.20, orange = 0.10, yellow = 0.10)

# Perform one-sample chi-square goodness-of-fit test:
test_result <- chisq.test(x = observed, p = p_expected)

# Display results:
test_result
```

``` output

	Chi-squared test for given probabilities

data:  observed
X-squared = 3.1, df = 5, p-value = 0.6846
```

*Interpretation*: The test yields χ² = 3.1 with a p-value = 0.685. We
fail to reject the null hypothesis", and there is no evidence to conclude a 
difference from the claimed distribution.


::::

::::spoiler

### One-sample z test

#### Used for

- Testing whether the mean of a single sample differs from a known population mean when the population standard deviation is known.  
- **Real-world example:** Checking if the average diameter of manufactured ball bearings equals the specified 5.00 cm when σ is known. This checks if the average is different, ie either smaller _or_ larger. We can also test if it _is_ smaller or larger.

#### Assumptions
- Sample is a simple random sample from the population.  
- Observations are independent.  
- Population standard deviation (σ) is known.  
- The sampling distribution of the mean is approximately normal (either the population is normal or n is large, e.g. ≥ 30).

#### Strengths
- More powerful than the t-test when σ is truly known.  
- Simple calculation and interpretation.  
- Relies on the normal distribution, which is well understood.

#### Weaknesses
- The true population σ is only very rarely known in practice.  
- Sensitive to departures from normality for small samples.  
- Misspecification of σ leads to incorrect inferences.

#### Example

##### Hypothesis
- **Null hypothesis (H₀):** The true mean diameter μ = 5.00 cm.  
- **Alternative hypothesis (H₁):** μ ≠ 5.00 cm.


``` r
# Sample of diameters (cm) for 25 ball bearings:
diameters <- c(5.03, 4.97, 5.01, 5.05, 4.99, 5.02, 5.00, 5.04, 4.96, 5.00,
               5.01, 4.98, 5.02, 5.03, 4.94, 5.00, 5.02, 4.99, 5.01, 5.03,
               4.98, 5.00, 5.04, 4.97, 5.02)

# Known population standard deviation:
sigma <- 0.05

# Hypothesized mean:
mu0 <- 5.00

# Compute test statistic:
n <- length(diameters)
xbar <- mean(diameters)
z_stat <- (xbar - mu0) / (sigma / sqrt(n))

# Two-sided p-value:
p_value <- 2 * (1 - pnorm(abs(z_stat)))

# Larger p-value
larger_p_value <- 1- pnorm(z_stat)

# Smaller p-value
smaller_p_value <- pnorm(z_stat)

# Output results:
z_stat; p_value; larger_p_value; smaller_p_value
```

``` output
[1] 0.44
```

``` output
[1] 0.6599371
```

``` output
[1] 0.3299686
```

``` output
[1] 0.6700314
```


Interpretation:
The sample mean is 5.004 cm. The z-statistic is 0.44 
with a two-sided p-value of 0.66. We
fail to reject the null hypothesis.
Thus, there is
no evidence to conclude a difference from the specified diameter of 5.00 cm.

We can similarly reject the hypothesis that the average diameter is larger (p = 0.3299686)
or that it is smaller (p = 0.6700314)

::::

::::spoiler

### One-sample t test

EJ KORREKTURLÆST!

Her kan vi nok med fordel bruge samme eksempel som i z-testen.

#### Used for
- Testing whether the mean of a single sample differs from a known or hypothesized population mean when the population standard deviation is unknown.  
- **Real-world example:** Determining if the average exam score of a class differs from the passing threshold of 70%.

#### Assumptions
- Sample is a simple random sample from the population.  
- Observations are independent.  
- The data are approximately normally distributed (especially important for small samples; n ≥ 30 reduces sensitivity).

#### Strengths
- Does not require knowing the population standard deviation.  
- Robust to mild departures from normality for moderate-to-large sample sizes.  
- Widely applicable and easily implemented.

#### Weaknesses
- Sensitive to outliers in small samples.  
- Performance degrades if normality assumption is seriously violated and n is small.  
- Degrees of freedom reduce power relative to z-test.

#### Example

##### Hypothesis

- **Null hypothesis (H₀):** The true mean exam score μ = 70.  
- **Alternative hypothesis (H₁):** μ ≠ 70.


``` r
# Sample of exam scores for 20 students:
scores <- c(68, 74, 71, 69, 73, 65, 77, 72, 70, 66,
            75, 68, 71, 69, 74, 67, 72, 70, 73, 68)

# Hypothesized mean:
mu0 <- 70

# Perform one-sample t-test:
test_result <- t.test(x = scores, mu = mu0)

# Display results:
test_result
```

``` output

	One Sample t-test

data:  scores
t = 0.84675, df = 19, p-value = 0.4077
alternative hypothesis: true mean is not equal to 70
95 percent confidence interval:
 69.1169 72.0831
sample estimates:
mean of x 
     70.6 
```

Interpretation:
The sample mean is 70.6. The t-statistic is 0.85 with 19 degrees of freedom and a two-sided p-value of 0.408. We
fail to reject the null hypothesis.
Thus, there is
no evidence to conclude the average score differs from the passing threshold of 70.

::::

::::spoiler

### One-sample Poisson test

EJ KORREKTURLÆST!

#### Used for
- Testing whether the observed count of events in a fixed period differs from a hypothesized Poisson rate.  
- **Real-world example:** Checking if the number of customer arrivals per hour at a call center matches the expected rate of 30 calls/hour.

#### Assumptions
- Events occur independently.  
- The rate of occurrence (λ) is constant over the observation period.  
- The count of events in non-overlapping intervals is independent.

#### Strengths
- Exact test based on the Poisson distribution (no large-sample approximation needed).  
- Valid for small counts and rare events.  
- Simple to implement in R via `poisson.test()`.

#### Weaknesses
- Sensitive to violations of the Poisson assumptions (e.g., overdispersion or time-varying rate).  
- Only assesses the overall rate, not the dispersion or clustering of events.  
- Cannot handle covariates or more complex rate structures.

#### Example

##### Hypothesis
- **Null hypothesis (H₀):** The event rate λ = 30 calls/hour.  
- **Alternative hypothesis (H₁):** λ ≠ 30 calls/hour.


``` r
# Observed number of calls in one hour:
observed_calls <- 36

# Hypothesized rate (calls per hour):
lambda0 <- 30

# Perform one-sample Poisson test (two-sided):
test_result <- poisson.test(x = observed_calls, T = 1, r = lambda0, alternative = "two.sided")

# Display results:
test_result
```

``` output

	Exact Poisson test

data:  observed_calls time base: 1
number of events = 36, time base = 1, p-value = 0.272
alternative hypothesis: true event rate is not equal to 30
95 percent confidence interval:
 25.21396 49.83917
sample estimates:
event rate 
        36 
```

Interpretation:
The test reports an observed count of 36 calls versus an expected 30 calls, yielding a p-value of 0.272. We
fail to reject the null hypothesis.
Thus, there is
no evidence to conclude the call rate differs from 30 calls/hour.

::::

::::spoiler

### Shapiro–Wilk test for normality

EJ KORREKTURLÆST

#### Used for
- Testing whether a sample comes from a normally distributed population.  
- **Real-world example:** Checking if the distribution of daily blood glucose measurements in a patient cohort is approximately normal.

#### Assumptions
- Observations are independent.  
- Data are continuous.  
- No extreme ties or many identical values.

#### Strengths
- Good power for detecting departures from normality in small to moderate samples (n ≤ 50).  
- Widely implemented and easy to run in R.  
- Provides both a test statistic (W) and p-value.

#### Weaknesses
- Very sensitive to even slight deviations from normality in large samples (n > 2000).  
- Does not indicate the nature of the departure (e.g., skewness vs. kurtosis).  
- Ties or repeated values can invalidate the test.

#### Example

##### Hypothesis
- **Null hypothesis (H₀):** The sample is drawn from a normal distribution.  
- **Alternative hypothesis (H₁):** The sample is not drawn from a normal distribution.


``` r
# Simulate a sample of 30 observations from a normal distribution:
set.seed(123)
sample_data <- rnorm(30, mean = 100, sd = 15)

# Perform Shapiro–Wilk test:
sw_result <- shapiro.test(sample_data)

# Display results:
sw_result
```

``` output

	Shapiro-Wilk normality test

data:  sample_data
W = 0.97894, p-value = 0.7966
```

Interpretation:
The Shapiro–Wilk statistic W = 0.979 with p-value = 0.797. We
fail to reject the null hypothesis.
Thus, there is
no evidence to conclude a departure from normality.

::::

::::spoiler

### Kolmogorov–Smirnov én-prøve-test (goodness-of-fit)

EJ KORREKTURLÆST

#### Used for
- Testing whether a sample comes from a specified continuous distribution.  
- **Real-world example:** Checking if patient systolic blood pressures follow a normal distribution with mean 120 mmHg and SD 15 mmHg.

#### Assumptions
- Observations are independent.  
- Data are continuous (no ties).  
- The null distribution is fully specified (parameters known, not estimated from the data).

#### Strengths
- Nonparametric: makes no assumption about distribution shape beyond continuity.  
- Sensitive to any kind of departure (location, scale, shape).  
- Exact distribution of the test statistic under H₀.

#### Weaknesses
- Requires that distribution parameters (e.g., mean, SD) are known a priori; if estimated from data, p-values are invalid.  
- Less powerful than parametric tests when the parametric form is correct.  
- Sensitive to ties and discrete data.

#### Example

##### Hypothesis
- **Null hypothesis (H₀):** The blood pressure values follow a Normal(μ = 120, σ = 15) distribution.  
- **Alternative hypothesis (H₁):** The blood pressure values do not follow Normal(120, 15).


``` r
set.seed(2025)
# Simulate systolic blood pressure for 40 patients:
sample_bp <- rnorm(40, mean = 120, sd = 15)

# Perform one-sample Kolmogorov–Smirnov test against N(120,15):
ks_result <- ks.test(sample_bp, "pnorm", mean = 120, sd = 15)

# Show results:
ks_result
```

``` output

	Exact one-sample Kolmogorov-Smirnov test

data:  sample_bp
D = 0.11189, p-value = 0.6573
alternative hypothesis: two-sided
```

Interpretation:
The KS statistic D = 0.112 with p-value = 0.657. We
fail to reject the null hypothesis.
Thus, there is
no evidence to conclude deviation from Normal(120,15).

::::

::::spoiler

### χ² goodness-of-fit test

EJ KORREKTURLÆST


#### Used for
- Testing whether observed categorical frequencies differ from expected categorical proportions.  
- **Real-world example:** Comparing the distribution of blood types in a sample of donors to known population proportions.

#### Assumptions
- Observations are independent.  
- Categories are mutually exclusive and exhaustive.  
- Expected count in each category is at least 5 for the chi-square approximation to hold.

#### Strengths
- Simple to compute and interpret.  
- Nonparametric: no requirement of normality.  
- Flexible for any number of categories.

#### Weaknesses
- Sensitive to small expected counts (invalidates approximation).  
- Doesn’t identify which categories drive the discrepancy without further post-hoc tests.  
- Requires independence—unsuitable for paired or repeated measures.

#### Example

##### Hypothesis
- **Null hypothesis (H₀):** The sample blood type proportions equal the known population proportions (A=0.42, B=0.10, AB=0.04, O=0.44).  
- **Alternative hypothesis (H₁):** At least one blood type proportion differs from its known value.


``` r
# Observed counts of blood types in 200 donors:
observed <- c(A = 85, B = 18, AB = 6, O = 91)

# Known population proportions:
p_expected <- c(A = 0.42, B = 0.10, AB = 0.04, O = 0.44)

# Perform chi-square goodness-of-fit test:
test_result <- chisq.test(x = observed, p = p_expected)

# Display results:
test_result
```

``` output

	Chi-squared test for given probabilities

data:  observed
X-squared = 0.81418, df = 3, p-value = 0.8461
```

Interpretation:
The test yields χ² = 0.81 with p-value = 0.846. We
fail to reject the null hypothesis.
Thus, there is
no evidence to conclude the sample proportions differ from the population.

::::

## To-prøve-tests og parrede tests



::::spoiler

### Two-sample F test for variance

EJ KORREKTURLÆST

We use this when we want to determine if two independent samples originate
from populations with the same variance.

#### Used for
- Testing whether two independent samples have equal variances.  
- **Real-world example:** Comparing the variability in systolic blood pressure measurements between two clinics.

#### Assumptions
- Both samples consist of independent observations.  
- Each sample is drawn from a normally distributed population.  
- Samples are independent of one another.

#### Strengths
- Simple calculation and interpretation.  
- Directly targets variance equality, a key assumption in many downstream tests (e.g., t-test).  
- Exact inference under normality.

#### Weaknesses
- Highly sensitive to departures from normality.  
- Only compares variance—doesn’t assess other distributional differences.  
- Not robust to outliers.

#### Example

##### Hypothesis
- **Null hypothesis (H₀):** σ₁² = σ₂² (the two population variances are equal).  
- **Alternative hypothesis (H₁):** σ₁² ≠ σ₂² (the variances differ).


``` r
# Simulate systolic BP (mmHg) from two clinics:
set.seed(2025)
clinicA <- rnorm(30, mean = 120, sd = 8)
clinicB <- rnorm(25, mean = 118, sd = 12)

# Perform two-sample F-test for variances:
f_result <- var.test(clinicA, clinicB, alternative = "two.sided")

# Display results:
f_result
```

``` output

	F test to compare two variances

data:  clinicA and clinicB
F = 0.41748, num df = 29, denom df = 24, p-value = 0.02606
alternative hypothesis: true ratio of variances is not equal to 1
95 percent confidence interval:
 0.1882726 0.8992623
sample estimates:
ratio of variances 
         0.4174837 
```

The F statistic is 0.417 with numerator df = 29 and denominator df = 24, and p-value = 0.0261. We


reject the null hypothesis.
Thus, there is
evidence that the variability in blood pressure differs between the two clinics.

::::

::::spoiler

### Paired t-test

EJ KORREKTURLÆST
#### Used for
- Testing whether the mean difference between two related (paired) samples differs from zero.  
- **Real-world example:** Comparing patients’ blood pressure before and after administering a new medication.

#### Assumptions
- Paired observations are independent of other pairs.  
- Differences between pairs are approximately normally distributed.  
- The scale of measurement is continuous (interval or ratio).

#### Strengths
- Controls for between‐subject variability by using each subject as their own control.  
- More powerful than unpaired tests when pairs are truly dependent.  
- Easy to implement and interpret.

#### Weaknesses
- Sensitive to outliers in the difference scores.  
- Requires that differences be approximately normal, especially for small samples.  
- Not appropriate if pairing is not justified or if missing data break pairs.

#### Example

##### Hypothesis
- **Null hypothesis (H₀):** The mean difference Δ = 0 (no change in blood pressure).  
- **Alternative hypothesis (H₁):** Δ ≠ 0 (blood pressure changes after medication).


``` r
# Simulated systolic blood pressure (mmHg) for 15 patients before and after treatment:
before <- c(142, 138, 150, 145, 133, 140, 147, 139, 141, 136, 144, 137, 148, 142, 139)
after  <- c(135, 132, 144, 138, 128, 135, 142, 133, 136, 130, 139, 132, 143, 137, 133)

# Perform paired t-test:
test_result <- t.test(before, after, paired = TRUE)

# Display results:
test_result
```

``` output

	Paired t-test

data:  before and after
t = 29.437, df = 14, p-value = 5.418e-14
alternative hypothesis: true mean difference is not equal to 0
95 percent confidence interval:
 5.19198 6.00802
sample estimates:
mean difference 
            5.6 
```

Interpretation:
The mean difference (before – after) is 5.6 mmHg. The t‐statistic is 29.44 with 14 degrees of freedom and a two‐sided p‐value of` r signif(test_result$p.value, 3)`. We
reject the null hypothesis.
Thus, there is
evidence that the medication significantly changed blood pressure.

::::

::::spoiler

### Two-sample t test (equal variances)

EJ KORREKTURLÆST

#### Used for
- Testing whether the means of two independent samples differ, assuming equal variances.  
- **Real-world example:** Comparing average systolic blood pressure between male and female patients when variability is similar.

#### Assumptions
- Observations in each group are independent.  
- Both populations are normally distributed (especially important for small samples).  
- The two populations have equal variances (homoscedasticity).

#### Strengths
- More powerful than Welch’s t-test when variances truly are equal.  
- Simple computation and interpretation via pooled variance.  
- Widely implemented and familiar to practitioners.

#### Weaknesses
- Sensitive to violations of normality in small samples.  
- Incorrect if variances differ substantially—can inflate Type I error.  
- Assumes homogeneity of variance, which may not hold in practice.

#### Example

##### Hypothesis
- **Null hypothesis (H₀):** μ₁ = μ₂ (the two population means are equal).  
- **Alternative hypothesis (H₁):** μ₁ ≠ μ₂ (the means differ).


``` r
set.seed(2025)
# Simulate systolic BP (mmHg):
groupA <- rnorm(25, mean = 122, sd = 10)  # e.g., males
groupB <- rnorm(25, mean = 118, sd = 10)  # e.g., females

# Perform two-sample t-test with equal variances:
test_result <- t.test(groupA, groupB, var.equal = TRUE)

# Display results:
test_result
```

``` output

	Two Sample t-test

data:  groupA and groupB
t = 2.6245, df = 48, p-value = 0.0116
alternative hypothesis: true difference in means is not equal to 0
95 percent confidence interval:
  1.733091 13.086377
sample estimates:
mean of x mean of y 
 125.2119  117.8021 
```

Interpretation:
The pooled estimate of the difference in means is 7.41 mmHg. The t-statistic is 2.62 with df = 48 and p-value = 0.0116. We
reject the null hypothesis.
Thus, there is
evidence that the average systolic blood pressure differs between the two groups.


::::

::::spoiler

### Two-sample t test (unequal variances)

EJ KORREKTURLÆST

#### Used for
- Testing whether the means of two independent samples differ when variances are unequal.  
- **Real-world example:** Comparing average recovery times for two different therapies when one therapy shows more variable outcomes.

#### Assumptions
- Observations in each group are independent.  
- Each population is approximately normally distributed (especially important for small samples).  
- Does **not** assume equal variances across groups.

#### Strengths
- Controls Type I error when variances differ.  
- More reliable than the pooled‐variance t‐test under heteroskedasticity.  
- Simple to implement via `t.test(..., var.equal = FALSE)` in R.

#### Weaknesses
- Slight loss of power compared to equal-variance t‐test when variances truly are equal.  
- Sensitive to departures from normality in small samples.  
- Degrees of freedom are approximated (Welch–Satterthwaite), which can reduce interpretability.

#### Example

##### Hypothesis
- **Null hypothesis (H₀):** μ₁ = μ₂ (the two population means are equal).  
- **Alternative hypothesis (H₁):** μ₁ ≠ μ₂ (the means differ).


``` r
set.seed(2025)
# Simulate recovery times (days) for two therapies:
therapyA <- rnorm(20, mean = 10, sd = 2)   # Therapy A
therapyB <- rnorm(25, mean = 12, sd = 4)   # Therapy B (more variable)

# Perform two-sample t-test with unequal variances:
test_result <- t.test(therapyA, therapyB, var.equal = FALSE)

# Display results:
test_result
```

``` output

	Welch Two Sample t-test

data:  therapyA and therapyB
t = -1.7792, df = 32.794, p-value = 0.08449
alternative hypothesis: true difference in means is not equal to 0
95 percent confidence interval:
 -3.8148079  0.2558875
sample estimates:
mean of x mean of y 
 10.41320  12.19266 
```

Interpretation:
The estimated difference in means is -1.78 days. The Welch t‐statistic is` r round(test_result$statistic, 2)` with df ≈ 32.8 and two‐sided p‐value =` r signif(test_result$p.value, 3`). We
fail to reject the null hypothesis.
Thus, there is
no evidence of a difference in average recovery times..

::::

::::spoiler

### Mann–Whitney U-test (Wilcoxon rank-sum)

EJ KORREKTURLÆST

#### Used for
- Comparing the central tendencies of two independent samples when the data are ordinal or not normally distributed.  
- **Real-world example:** Testing whether pain scores (0–10) differ between patients receiving Drug A versus Drug B when scores are skewed.

#### Assumptions
- Observations are independent both within and between groups.  
- The response variable is at least ordinal.  
- The two distributions have the same shape (so that differences reflect location shifts).

#### Strengths
- Nonparametric: does not require normality or equal variances.  
- Robust to outliers and skewed data.  
- Simple rank-based calculation.

#### Weaknesses
- Less powerful than t-test when data are truly normal.  
- If distributions differ in shape as well as location, interpretation becomes ambiguous.  
- Only tests for location shift, not differences in dispersion.

#### Example

##### Hypothesis
- **Null hypothesis (H₀):** The distributions of pain scores are identical for Drug A and Drug B.  
- **Alternative hypothesis (H₁):** The distributions differ in location (median pain differs between drugs).


``` r
# Simulate pain scores (0–10) for two independent groups:
set.seed(2025)
drugA <- c(2,3,4,5,4,3,2,6,5,4,  # skewed lower
           3,4,5,4,3,2,3,4,5,3)
drugB <- c(4,5,6,7,6,5,7,8,6,7,  # skewed higher
           6,7,5,6,7,6,8,7,6,7)

# Perform Mann–Whitney U test (Wilcoxon rank-sum):
mw_result <- wilcox.test(drugA, drugB, alternative = "two.sided", exact = FALSE)

# Display results:
mw_result
```

``` output

	Wilcoxon rank sum test with continuity correction

data:  drugA and drugB
W = 20.5, p-value = 9.123e-07
alternative hypothesis: true location shift is not equal to 0
```


Interpretation:
The Wilcoxon rank-sum statistic W = 20.5 with p-value = 9.12\times 10^{-7}. We
reject the null hypothesis.
Thus, there is
evidence that median pain scores differ between Drug A and Drug B.

::::

::::spoiler

### Wilcoxon signed-rank test

EJ KORREKTURLÆST

#### Used for
- Testing whether the median difference between paired observations is zero.  
- **Real-world example:** Comparing patients’ pain scores before and after a new analgesic treatment when differences may not be normally distributed.

#### Assumptions
- Observations are paired and the pairs are independent.  
- Differences are at least ordinal and symmetrically distributed around the median.  
- No large number of exact zero differences (ties).

#### Strengths
- Nonparametric: does not require normality of differences.  
- Controls for within‐subject variability by using paired design.  
- Robust to outliers in the paired differences.

#### Weaknesses
- Less powerful than the paired t-test when differences are truly normal.  
- Requires symmetry of the distribution of differences.  
- Cannot easily handle many tied differences.

#### Example

##### Hypothesis
- **Null hypothesis (H₀):** The median difference in pain score (before – after) = 0 (no change).  
- **Alternative hypothesis (H₁):** The median difference ≠ 0 (pain changes after treatment).


``` r
# Simulated pain scores (0–10) for 12 patients:
before <- c(6, 7, 5, 8, 6, 7, 9, 5, 6, 8, 7, 6)
after  <- c(4, 6, 5, 7, 5, 6, 8, 4, 5, 7, 6, 5)

# Perform Wilcoxon signed-rank test:
wsr_result <- wilcox.test(before, after, paired = TRUE, 
                          alternative = "two.sided", exact = FALSE)

# Display results:
wsr_result
```

``` output

	Wilcoxon signed rank test with continuity correction

data:  before and after
V = 66, p-value = 0.001586
alternative hypothesis: true location shift is not equal to 0
```

Interpretation:
The Wilcoxon signed‐rank test statistic V =66 with p-value =` r signif(wsr_result$p.value, 3)`. We
reject the null hypothesis.
Thus, there is
evidence that median pain scores change after treatment..

::::

::::spoiler

### Kolmogorov–Smirnov two-sample-test

EJ KORREKTURLÆST

#### Used for
- Testing whether two independent samples come from the same continuous distribution.  
- **Real-world example:** Comparing the distribution of recovery times for patients receiving Drug A versus Drug B.

#### Assumptions
- Observations in each sample are independent.  
- Data are continuous with no ties.  
- The two samples are drawn from fully specified continuous distributions (no parameters estimated from the same data).

#### Strengths
- Nonparametric: makes no assumption about the shape of the distribution.  
- Sensitive to differences in location, scale, or overall shape.  
- Exact distribution under the null when samples are not too large.

#### Weaknesses
- Less powerful than parametric alternatives if the true form is known (e.g., t-test for normal data).  
- Invalid p-values if there are ties or discrete data.  
- Does not indicate how distributions differ—only that they do.

#### Example

##### Hypothesis
- **Null hypothesis (H₀):** The two samples come from the same distribution.  
- **Alternative hypothesis (H₁):** The two samples come from different distributions.


``` r
# Simulate recovery times (days) for two therapies:
set.seed(2025)
therapyA <- rnorm(30, mean = 10, sd = 2)   # Therapy A
therapyB <- rnorm(30, mean = 12, sd = 3)   # Therapy B

# Perform two-sample Kolmogorov–Smirnov test:
ks_result <- ks.test(therapyA, therapyB, alternative = "two.sided", exact = FALSE)

# Display results:
ks_result
```

``` output

	Asymptotic two-sample Kolmogorov-Smirnov test

data:  therapyA and therapyB
D = 0.43333, p-value = 0.007153
alternative hypothesis: two-sided
```

Interpretation:
The KS statistic D = 0.433 with p-value = 0.00715. We
reject the null hypothesis.
Thus, there is
evidence that the distribution of recovery times differs between therapies.

::::

::::spoiler
### Levene’s test for homoskedasticitet

EJ KORREKTURLÆST

#### Used for
- Testing whether multiple groups have equal variances.  
- **Real-world example:** Checking if the variability in patient blood pressure differs between three different clinics.

#### Assumptions
- Observations are independent.  
- The underlying distributions within each group are approximately symmetric (Levene’s test is robust to non-normality but assumes no extreme skew).

#### Strengths
- More robust to departures from normality than Bartlett’s test.  
- Applicable to two or more groups.  
- Simple to implement and interpret.

#### Weaknesses
- Less powerful than tests that assume normality when data truly are normal.  
- Can be sensitive to extreme outliers despite its robustness.  
- Does not indicate which groups differ in variance without follow-up comparisons.

#### Example

##### Hypothesis
- **Null hypothesis (H₀):** All groups have equal variances (σ₁² = σ₂² = … = σₖ²).  
- **Alternative hypothesis (H₁):** At least one group’s variance differs.


``` r
# Simulate data for three groups (n = 10 each):
set.seed(123)
group   <- factor(rep(c("ClinicA", "ClinicB", "ClinicC"), each = 10))
scores  <- c(rnorm(10, mean = 120, sd = 5),
             rnorm(10, mean = 120, sd = 8),
             rnorm(10, mean = 120, sd = 5))
df      <- data.frame(group, scores)

# Perform Levene’s test for homogeneity of variances:
library(car)
```

``` output
Loading required package: carData
```

``` r
levene_result <- leveneTest(scores ~ group, data = df)

# Show results:
levene_result
```

``` output
Levene's Test for Homogeneity of Variance (center = median)
      Df F value Pr(>F)
group  2   0.872 0.4296
      27               
```

Interpretation:
Levene’s test yields an F-statistic of 0.87 
with a p-value of 0.43. We
fail to reject the null hypothesis.
This means there is
no evidence of differing variances across clinics.







::::

::::spoiler

### Bartlett’s test for homoskedasticitet

EJ KORREKTURLÆST

#### Used for
- Testing whether multiple groups have equal variances under the assumption of normality.  
- **Real-world example:** Checking if the variability in laboratory test results differs across three different laboratories.

#### Assumptions
- Observations within each group are independent.  
- Each group is drawn from a normally distributed population.  
- Groups are independent of one another.

#### Strengths
- More powerful than Levene’s test when normality holds.  
- Directly targets equality of variances under the normal model.  
- Simple to compute in R via `bartlett.test()`.

#### Weaknesses
- Highly sensitive to departures from normality—small deviations can inflate Type I error.  
- Does not indicate which groups differ without further pairwise testing.  
- Not robust to outliers.

#### Example

##### Hypothesis
- **Null hypothesis (H₀):** All group variances are equal (σ₁² = σ₂² = σ₃²).  
- **Alternative hypothesis (H₁):** At least one group variance differs.


``` r
# Simulate data for three laboratories (n = 12 each):
set.seed(456)
lab      <- factor(rep(c("LabA", "LabB", "LabC"), each = 12))
values   <- c(rnorm(12, mean = 100, sd = 5),
              rnorm(12, mean = 100, sd = 8),
              rnorm(12, mean = 100, sd = 5))
df       <- data.frame(lab, values)

# Perform Bartlett’s test for homogeneity of variances:
bartlett_result <- bartlett.test(values ~ lab, data = df)

# Display results:
bartlett_result
```

``` output

	Bartlett test of homogeneity of variances

data:  values by lab
Bartlett's K-squared = 10.387, df = 2, p-value = 0.005552
```


Interpretation:
Bartlett’s K-squared = 10.39 with df = 2 and p-value = 0.00555. We
reject the null hypothesis.
Thus, there is
evidence that at least one laboratory’s variance differs from the others.


::::



## Variansanalyse (ANOVA/ANCOVA)

::::spoiler
### One-way ANOVA

EJ KORREKTURLÆST

#### Used for
- Testing whether the means of three or more independent groups differ.  
- **Real-world example:** Comparing average test scores among students taught by three different teaching methods.

#### Assumptions
- Observations are independent.  
- Each group’s residuals are approximately normally distributed.  
- Homogeneity of variances across groups.

#### Strengths
- Controls Type I error rate when comparing multiple groups.  
- Simple to compute and interpret via F-statistic.  
- Foundation for many extensions (e.g., factorial ANOVA, mixed models).

#### Weaknesses
- Sensitive to heterogeneity of variances, especially with unequal group sizes.  
- Only tells you that at least one mean differs—does not indicate which groups differ without post-hoc tests.  
- Assumes normality; moderately robust for large samples, but small samples can be problematic.

#### Example

##### Hypothesis
- **Null hypothesis (H₀):** μ₁ = μ₂ = μ₃ (all three group means are equal).  
- **Alternative hypothesis (H₁):** At least one group mean differs.


``` r
# Simulate exam scores for three teaching methods (n = 20 each):
set.seed(2025)
method <- factor(rep(c("Lecture", "Online", "Hybrid"), each = 20))
scores <- c(rnorm(20, mean = 75, sd = 8),
            rnorm(20, mean = 80, sd = 8),
            rnorm(20, mean = 78, sd = 8))
df     <- data.frame(method, scores)

# Fit one-way ANOVA:
anova_fit <- aov(scores ~ method, data = df)

# Summarize ANOVA table:
anova_summary <- summary(anova_fit)
anova_summary
```

``` output
            Df Sum Sq Mean Sq F value Pr(>F)
method       2    146   72.79   1.184  0.314
Residuals   57   3505   61.48               
```

Interpretation:
The ANOVA yields F = 1.18 with df₁ =` r anova_summary[[1]]["method","Df"]` and df₂ =` r anova_summary[[1]]["Residuals","Df"]`, and p-value = 0.314. We
fail to reject the null hypothesis.
Thus, there is
no evidence of a difference in mean scores among methods.

::::

::::spoiler
### One-way ANCOVA

EJ KORREKTURLÆST

#### Used for
- Comparing group means on a continuous outcome while adjusting for one continuous covariate.  
- **Real-world example:** Evaluating whether three different teaching methods lead to different final exam scores after accounting for students’ prior GPA.

#### Assumptions
- Observations are independent.  
- The relationship between the covariate and the outcome is linear and the same across groups (homogeneity of regression slopes).  
- Residuals are normally distributed with equal variances across groups.  
- Covariate is measured without error and is independent of group assignment.

#### Strengths
- Removes variability due to the covariate, increasing power to detect group differences.  
- Controls for confounding by the covariate.  
- Simple extension of one-way ANOVA with interpretation familiar to ANOVA users.

#### Weaknesses
- Sensitive to violation of homogeneity of regression slopes.  
- Mis‐specification of the covariate‐outcome relationship biases results.  
- Requires accurate measurement of the covariate.  
- Does not accommodate multiple covariates without extension to factorial ANCOVA or regression.

#### Example

##### Hypothesis
- **Null hypothesis (H₀):** After adjusting for prior GPA, the mean final exam scores are equal across the three teaching methods (μ_Lecture = μ_Online = μ_Hybrid).  
- **Alternative hypothesis (H₁):** At least one adjusted group mean differs.


``` r
set.seed(2025)
n <- 20
method <- factor(rep(c("Lecture","Online","Hybrid"), each = n))
prior_gpa <- rnorm(3*n, mean = 3.0, sd = 0.3)

# Simulate final exam scores with a covariate effect:
# true intercepts 75, 78, 80; slope = 5 points per GPA unit; noise sd = 5
final_score <- 75 + 
               ifelse(method=="Online", 3, 0) + 
               ifelse(method=="Hybrid", 5, 0) + 
               5 * prior_gpa + 
               rnorm(3*n, sd = 5)

df <- data.frame(method, prior_gpa, final_score)

# Fit one-way ANCOVA:
ancova_fit <- aov(final_score ~ prior_gpa + method, data = df)
ancova_summary <- summary(ancova_fit)
ancova_summary
```

``` output
            Df Sum Sq Mean Sq F value  Pr(>F)   
prior_gpa    1  166.5   166.5   5.967 0.01775 * 
method       2  296.8   148.4   5.320 0.00767 **
Residuals   56 1562.4    27.9                   
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
```

Interpretation:
After adjusting for prior GPA, the effect of teaching method yields F = 5.32 (df₁ =` r ancova_summary[[1]]["method","Df"]`, df₂ =` r ancova_summary[[1]]["Residuals","Df"]`) with p =` r signif(ancova_summary[[1]]["method","Pr(>F)"], 3`). We
reject the null hypothesis.
Thus, there is
evidence that, controlling for prior GPA, at least one teaching method leads to a different average final score..



::::

::::spoiler
### Welch’s ANOVA (uden antagelse om lige varianser)

EJ KORREKTURLÆST

#### Used for
- Testing whether the means of three or more independent groups differ when variances are unequal.  
- **Real-world example:** Comparing average systolic blood pressure across three clinics known to have different measurement variability.

#### Assumptions
- Observations are independent.  
- Each group’s residuals are approximately normally distributed.  
- Does **not** assume equal variances across groups.

#### Strengths
- Controls Type I error under heteroskedasticity better than ordinary ANOVA.  
- Simple to implement via `oneway.test(..., var.equal = FALSE)`.  
- More powerful than nonparametric alternatives when normality holds.

#### Weaknesses
- Sensitive to departures from normality, especially with small sample sizes.  
- Does not provide post-hoc comparisons by default; requires additional tests.  
- Still assumes independence and approximate normality within each group.

#### Example

##### Hypothesis
- **Null hypothesis (H₀):** All group means are equal (μ₁ = μ₂ = μ₃).  
- **Alternative hypothesis (H₁):** At least one group mean differs.


``` r
set.seed(2025)
# Simulate systolic BP (mmHg) for three clinics with unequal variances:
clinic   <- factor(rep(c("A","B","C"), times = c(15, 20, 12)))
bp_values <- c(
  rnorm(15, mean = 120, sd = 5),
  rnorm(20, mean = 125, sd = 10),
  rnorm(12, mean = 118, sd = 7)
)
df <- data.frame(clinic, bp_values)

# Perform Welch’s one-way ANOVA:
welch_result <- oneway.test(bp_values ~ clinic, data = df, var.equal = FALSE)

# Display results:
welch_result
```

``` output

	One-way analysis of means (not assuming equal variances)

data:  bp_values and clinic
F = 2.7802, num df = 2.000, denom df = 23.528, p-value = 0.08244
```

Interpretation:
The Welch statistic = 2.78 with df ≈ 2, 23.53, and p-value = 0.0824. We
fail to reject the null hypothesis.
Thus, there is
no evidence of a difference in mean blood pressure among the clinics.

::::

::::spoiler

### Repeated-measures ANOVA

EJ KORREKTURLÆST

#### Used for
- Testing whether the means of three or more related (within‐subject) conditions differ.  
- **Real-world example:** Assessing whether students’ reaction times change across three levels of sleep deprivation (0 h, 12 h, 24 h) measured on the same individuals.

#### Assumptions
- Observations (subjects) are independent.  
- The dependent variable is approximately normally distributed in each condition.  
- **Sphericity:** variances of the pairwise differences between conditions are equal.

#### Strengths
- Controls for between‐subject variability by using each subject as their own control.  
- More powerful than independent‐groups ANOVA when measures are correlated.  
- Can model complex within‐subject designs (e.g. time × treatment interactions).

#### Weaknesses
- Sensitive to violations of sphericity (inflates Type I error).  
- Missing data in any condition drops the entire subject (unless using more advanced mixed‐model methods).  
- Interpretation can be complex when there are many levels or interactions.

#### Example

##### Hypothesis
- **Null hypothesis (H₀):** The mean reaction time is the same across 0 h, 12 h, and 24 h sleep deprivation.  
- **Alternative hypothesis (H₁):** At least one condition’s mean reaction time differs.


``` r
set.seed(2025)
n_subj <- 12
subject <- factor(rep(1:n_subj, each = 3))
condition <- factor(rep(c("0h","12h","24h"), times = n_subj))
# Simulate reaction times (ms):
rt <- c(rnorm(n_subj, mean = 300, sd = 20),
        rnorm(n_subj, mean = 320, sd = 20),
        rnorm(n_subj, mean = 340, sd = 20))
df <- data.frame(subject, condition, rt)

# Fit repeated-measures ANOVA:
rm_fit <- aov(rt ~ condition + Error(subject/condition), data = df)
rm_summary <- summary(rm_fit)

# Display results:
rm_summary
```

``` output

Error: subject
          Df Sum Sq Mean Sq F value Pr(>F)
Residuals 11   9892   899.3               

Error: subject:condition
          Df Sum Sq Mean Sq F value Pr(>F)
condition  2   1000     500   1.188  0.324
Residuals 22   9262     421               
```

Interpretation:
The within‐subjects effect of sleep deprivation yields F =  1.19 with df₁ = 2 and df₂ = `r
rm_summary[[2]][[1]]["Residuals","Df"]`, p = 0.324. We
fail to reject the null hypothesis.
Thus, there is
no evidence that reaction times differ across sleep deprivation conditions.


::::

::::spoiler

### MANOVA

EJ KORREKTURLÆST

#### Used for
- Testing for differences on multiple continuous dependent variables across one or more grouping factors simultaneously.  
- **Real-world example:** Evaluating whether three different diets lead to different patterns of weight loss and cholesterol reduction.

#### Assumptions
- Multivariate normality of the dependent variables within each group.  
- Homogeneity of covariance matrices across groups.  
- Observations are independent.  
- Linear relationships among dependent variables.

#### Strengths
- Controls family-wise Type I error by testing all DVs together.  
- Can detect patterns that univariate ANOVAs might miss.  
- Provides multiple test statistics (Pillai, Wilks, Hotelling–Lawley, Roy) for flexibility.

#### Weaknesses
- Sensitive to violations of multivariate normality and homogeneity of covariances.  
- Requires larger sample sizes as the number of DVs increases.  
- Interpretation can be complex; follow-up analyses often needed to determine which DVs drive effects.

#### Example

##### Hypothesis
- **Null hypothesis (H₀):** The vector of means for weight loss and cholesterol change is equal across the three diet groups.  
- **Alternative hypothesis (H₁):** At least one diet group differs on the combination of weight loss and cholesterol change.


``` r
set.seed(2025)
n_per_group <- 15
diet <- factor(rep(c("LowFat", "LowCarb", "Mediterranean"), each = n_per_group))

# Simulate outcomes:
weight_loss <- c(rnorm(n_per_group, mean = 5,  sd = 1.5),
                 rnorm(n_per_group, mean = 8,  sd = 1.5),
                 rnorm(n_per_group, mean = 7,  sd = 1.5))
cholesterol  <- c(rnorm(n_per_group, mean = 10, sd = 2),
                 rnorm(n_per_group, mean = 12, sd = 2),
                 rnorm(n_per_group, mean = 9,  sd = 2))

df <- data.frame(diet, weight_loss, cholesterol)

# Fit MANOVA:
manova_fit <- manova(cbind(weight_loss, cholesterol) ~ diet, data = df)

# Summarize using Pillai’s trace:
manova_summary <- summary(manova_fit, test = "Pillai")
manova_summary
```

``` output
          Df  Pillai approx F num Df den Df    Pr(>F)    
diet       2 0.58953   8.7773      4     84 5.708e-06 ***
Residuals 42                                             
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
```

Interpretation:
Pillai’s trace = 0.59, F = 8.78 with df = 42, and p-value = 5.71\times 10^{-6}. We
reject the null hypothesis.
This indicates that there is

a significant multivariate effect of diet on weight loss and cholesterol change.



Pillai?


Der er fire almindeligt brugte taststatistikker i MANOVA. Pillai, Wilks' lambd,
Hotelling-Lawleys trace og Roys largest root.

Pillai’s trace (også kaldet Pillai–Bartlett’s trace) er én af fire almindeligt brugte multivariate test-statistikker i MANOVA (ud over Wilks’ lambda, Hotelling–Lawley’s trace og Roy’s largest root). Kort om Pillai:

Definition: Summen af egenværdierne divideret med (1 + egenværdierne) for hver kanonisk variabel. Det giver en samlet målestok for, hvor stor en andel af den totale variation der forklares af gruppetilhørsforholdet på tværs af alle afhængige variable.

Fortolkning: Højere Pillai-værdi (op til 1) indikerer stærkere multivariat effekt.

Hvorfor vælge Pillai?

Robusthed: Pillai’s trace er den mest robuste over for overtrædelser af antagelserne om homogen kovarians og multivariat normalitet. Hvis dine data har ulige gruppe-størrelser eller let skæve fordelinger, er Pillai ofte det sikreste valg.

Type I-kontrol: Den holder typisk kontrol med falsk positive (Type I-fejl) bedre end de andre, når antagelser brydes.

Er det altid det bedste valg?

Ikke nødvendigvis. Hvis dine data strengt opfylder antagelserne (multivariat normalitet, homogen kovarians og rimeligt store, ensartede grupper), kan de andre statistikker være mere “kraftfulde” (dvs. give større chance for at opdage en ægte effekt).

Wilks’ lambda er mest brugt i traditionel litteratur og har ofte god power under idéelle forhold.

Hotelling–Lawley’s trace kan være særligt følsom, når få kanoniske dimensioner bærer meget af effekten.

Roy’s largest root er ekstremt kraftfuld, hvis kun én kanonisk variabel adskiller grupperne, men er også mest sårbar gruppe-størrelser.over for antagelsesovertrædelser.

Kort anbefaling:

Brug Pillai’s trace som standard, især hvis du er usikker på antagelsesopfyldelsen eller har små/ulige 

Overvej Wilks’ lambda eller andre, hvis dine data opfylder alle antagelser solidt, og du ønsker maksimal statistisk power.

Tjek altid flere tests; hvis de konkluderer ens, styrker det din konklusion.


#### Wilks’ lambda  
- **Definition:** Ratio of the determinant of the within‐groups SSCP (sum of squares and cross‐products) matrix to the determinant of the total SSCP matrix:  
  \[
    \Lambda = \frac{\det(W)}{\det(T)} = \prod_{i=1}^s \frac{1}{1 + \lambda_i}
  \]  
  hvor \(\lambda_i\) er de canoniske egenværdier.  
- **Fortolkning:** Værdier nær 0 indikerer stor multivariat effekt (mellem‐grupper‐variation >> inden‐gruppe‐variation), værdier nær 1 indikerer lille eller ingen effekt.  
- **Styrker:**  
  - Klassisk og mest udbredt i litteraturen.  
  - God power under ideal antagelsesopfyldelse (multivariat normalitet, homogene kovarianser).  
- **Svagheder:**  
  - Mindre robust ved skæve fordelinger eller ulige gruppe‐størrelser.  
  - Kan undervurdere effektstørrelse, hvis én eller flere kanoniske variabler bærer effekten ujævnt.  
- **Anbefaling:**  
  - Brug Wilks’ lambda, når du er sikker på, at antagelserne er opfyldt, og du ønsker en velkendt statistisk test med god power under idealforhold.

#### Hotelling–Lawley’s trace  
- **Definition:** Summen af de canoniske egenværdier:  
  \[
    T = \sum_{i=1}^s \lambda_i
  \]  
- **Fortolkning:** Højere værdi betyder større samlet multivariat effekt.  
- **Styrker:**  
  - Sensitiv over for effekter fordelt over flere kanoniske dimensioner.  
  - Kan være mere kraftfuld end Wilks’ lambda, hvis flere dimensioner bidrager til forskellen.  
- **Svagheder:**  
  - Mindre robust over for antagelsesbrud end Pillai’s trace.  
  - Kan overvurdere effektstørrelse, hvis én dimension dominerer kraftigt.  
- **Anbefaling:**  
  - Overvej Hotelling–Lawley’s trace, når du forventer, at effekten spreder sig over flere kanoniske variabler, og antagelserne er rimeligt dækket.

#### Roy’s largest root  
- **Definition:** Den største canoniske egenværdi alene:  
  \[
    \Theta = \max_i \lambda_i
  \]  
- **Fortolkning:** Måler den stærkeste enkeltdimensionseffekt.  
- **Styrker:**  
  - Højest power, når én kanonisk variabel står for størstedelen af gruppedifferensen.  
  - Let at beregne og fortolke, hvis fokus er på “den stærkeste effekt”.  
- **Svagheder:**  
  - Meget følsom over for antagelsesbrud (normalitet, homogene kovarianser).  
  - Ikke informativ, hvis flere dimensioner bidrager jævnt.  
- **Anbefaling:**  
  - Brug Roy’s largest root, når du har en stærk a priori mistanke om én dominerende kanonisk dimension og er komfortabel med forudsætningerne.

> **Tips:** Sammenlign altid flere test‐statistikker – hvis de peger i samme retning, styrker det din konklusion. Pillai’s trace er generelt mest robust, Wilks’ lambda mest almindelig, Hotelling–Lawley god til flere dimensioner, og Roy’s largest root bedst, når én dimension dominerer.  






::::

::::spoiler


### Friedman test (nonparametrisk gentagne målinger)

EJ KORREKTURLÆST

#### Used for
- Testing for differences in central tendency across three or more related (paired) groups when assumptions of repeated‐measures ANOVA are violated.  
- **Real-world example:** Comparing median pain scores at baseline, 1 hour, and 24 hours after surgery in the same patients.

#### Assumptions
- Observations are paired and the sets of scores for each condition are related (e.g., repeated measures on the same subjects).  
- Data are at least ordinal.  
- The distribution of differences across pairs need not be normal.

#### Strengths
- Nonparametric: does not require normality or sphericity.  
- Controls for between‐subject variability by using each subject as their own block.  
- Robust to outliers and skewed data.

#### Weaknesses
- Less powerful than repeated‐measures ANOVA when normality and sphericity hold.  
- Only indicates that at least one condition differs—post‐hoc tests are needed to locate differences.  
- Assumes similar shaped distributions across conditions.

#### Example

##### Hypothesis
- **Null hypothesis (H₀):** The distributions of scores are the same across all conditions (no median differences).  
- **Alternative hypothesis (H₁):** At least one condition’s distribution (median) differs.


``` r
# Simulate pain scores (0–10) for 12 patients at 3 time points:
set.seed(2025)
patient   <- factor(rep(1:12, each = 3))
timepoint <- factor(rep(c("Baseline","1hr","24hr"), times = 12))
scores    <- c(
  rpois(12, lambda = 5),   # Baseline
  rpois(12, lambda = 3),   # 1 hour post-op
  rpois(12, lambda = 4)    # 24 hours post-op
)
df <- data.frame(patient, timepoint, scores)

# Perform Friedman test:
friedman_result <- friedman.test(scores ~ timepoint | patient, data = df)

# Display results:
friedman_result
```

``` output

	Friedman rank sum test

data:  scores and timepoint and patient
Friedman chi-squared = 1.6364, df = 2, p-value = 0.4412
```

Interpretation:
The Friedman chi-squared = 1.64 with df = 2 and p-value = 0.441. We
fail to reject the null hypothesis.
Thus, there is
no evidence that pain scores differ across time points.

::::

::::spoiler

### Post-hoc: Tukey HSD

EJ KORREKTURLÆST

#### Used for
- Performing pairwise comparisons of group means after a significant one‐way ANOVA to identify which groups differ.  
- **Real-world example:** Determining which teaching methods (Lecture, Online, Hybrid) differ in average exam scores after finding an overall effect.

#### Assumptions
- A significant one‐way ANOVA has been obtained.  
- Observations are independent.  
- Residuals from the ANOVA are approximately normally distributed.  
- Homogeneity of variances across groups (though Tukey’s HSD is fairly robust).

#### Strengths
- Controls the family‐wise error rate across all pairwise tests.  
- Provides confidence intervals for each mean difference.  
- Widely available and simple to interpret.

#### Weaknesses
- Requires balanced or nearly balanced designs for optimal power.  
- Less powerful than some alternatives if variances are highly unequal.  
- Only applies after a significant omnibus ANOVA.

#### Example

##### Hypothesis
- **Null hypothesis (H₀):** All pairwise mean differences between teaching methods are zero (e.g., μ_Lecture – μ_Online = 0, etc.).  
- **Alternative hypothesis (H₁):** At least one pairwise mean difference ≠ 0.


``` r
set.seed(2025)
# Simulate exam scores for three teaching methods (n = 20 each):
method <- factor(rep(c("Lecture", "Online", "Hybrid"), each = 20))
scores <- c(rnorm(20, mean = 75, sd = 8),
            rnorm(20, mean = 80, sd = 8),
            rnorm(20, mean = 78, sd = 8))
df     <- data.frame(method, scores)

# Fit one-way ANOVA:
anova_fit <- aov(scores ~ method, data = df)

# Perform Tukey HSD post-hoc:
tukey_result <- TukeyHSD(anova_fit, "method")

# Display results:
tukey_result
```

``` output
  Tukey multiple comparisons of means
    95% family-wise confidence level

Fit: aov(formula = scores ~ method, data = df)

$method
                    diff       lwr      upr     p adj
Lecture-Hybrid -2.542511 -8.509478 3.424457 0.5640694
Online-Hybrid   1.192499 -4.774468 7.159467 0.8805866
Online-Lecture  3.735010 -2.231958 9.701978 0.2956108
```

Interpretation:
Each row of the output gives the estimated difference in means, a 95% confidence 
interval, and an adjusted p‐value. For example, if the Lecture–Online comparison shows a mean difference of –5.0 (95% CI: –8.0 to –2.0, p adj = 0.002), we conclude that the Online method yields significantly higher scores than Lecture. Comparisons with p adj < 0.05 indicate significant mean differences between those teaching methods.

::::

::::spoiler

### Post-hoc: Dunnett’s test

EJ KORREKTURLÆST

#### Used for
- Comparing multiple treatment groups to a single control while controlling the family‐wise error rate.  
- **Real-world example:** Testing whether two new fertilizers (Fertilizer A, Fertilizer B) improve crop yield compared to the standard fertilizer (Control).

#### Assumptions
- Observations are independent.  
- Residuals from the ANOVA are approximately normally distributed.  
- Homogeneity of variances across groups.  
- A significant overall ANOVA (omnibus F-test) has been observed or intended.

#### Strengths
- Controls the family-wise error rate when making multiple comparisons to a control.  
- More powerful than Tukey HSD when only control comparisons are of interest.  
- Provides simultaneous confidence intervals and adjusted p-values.

#### Weaknesses
- Only compares each group to the control; does not test all pairwise contrasts.  
- Sensitive to violations of normality and homogeneity of variances.  
- Requires a pre-specified control group.

#### Example

##### Hypothesis
- **Null hypothesis (H₀):** Each treatment mean equals the control mean (e.g., μ_A = μ_Control, μ_B = μ_Control).  
- **Alternative hypothesis (H₁):** At least one treatment mean differs from the control mean (e.g., μ_A ≠ μ_Control, μ_B ≠ μ_Control).


``` r
# Simulate crop yields (kg/plot) for Control and two new fertilizers:
set.seed(2025)
treatment <- factor(rep(c("Control", "FertilizerA", "FertilizerB"), each = 20))
yield     <- c(
  rnorm(20, mean = 50, sd = 5),   # Control
  rnorm(20, mean = 55, sd = 5),   # Fertilizer A
  rnorm(20, mean = 53, sd = 5)    # Fertilizer B
)
df <- data.frame(treatment, yield)

# Fit one-way ANOVA:
fit_anova <- aov(yield ~ treatment, data = df)

# Perform Dunnett's test (each treatment vs. Control):
library(multcomp)
```

``` output
Loading required package: mvtnorm
```

``` output
Loading required package: survival
```

``` output
Loading required package: TH.data
```

``` output
Loading required package: MASS
```

``` output

Attaching package: 'TH.data'
```

``` output
The following object is masked from 'package:MASS':

    geyser
```

``` output

Attaching package: 'multcomp'
```

``` output
The following object is masked _by_ '.GlobalEnv':

    cholesterol
```

``` r
dunnett_result <- glht(fit_anova, linfct = mcp(treatment = "Dunnett"))

# Summary with adjusted p-values:
summary(dunnett_result)
```

``` output

	 Simultaneous Tests for General Linear Hypotheses

Multiple Comparisons of Means: Dunnett Contrasts


Fit: aov(formula = yield ~ treatment, data = df)

Linear Hypotheses:
                           Estimate Std. Error t value Pr(>|t|)  
FertilizerA - Control == 0    4.209      1.550   2.716   0.0165 *
FertilizerB - Control == 0    2.714      1.550   1.751   0.1503  
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
(Adjusted p values reported -- single-step method)
```

``` r
confint(dunnett_result)
```

``` output

	 Simultaneous Confidence Intervals

Multiple Comparisons of Means: Dunnett Contrasts


Fit: aov(formula = yield ~ treatment, data = df)

Quantile = 2.2682
95% family-wise confidence level
 

Linear Hypotheses:
                           Estimate lwr     upr    
FertilizerA - Control == 0  4.2094   0.6942  7.7246
FertilizerB - Control == 0  2.7141  -0.8011  6.2292
```

Interpretation:
The Dunnett contrasts compare each fertilizer to Control. For example, if the contrast FertilizerA–Control shows an estimate of r round(coef(dunnett_result)[1], 2) kg with a 95% simultaneous CI [4.21, 0.69] and adjusted p-value = 0.0165, we
reject the null for Fertilizer A vs. Control—i.e., Fertilizer A yields significantly different crop output.
Similarly, for FertilizerB vs. Control (contrast index 2), the estimate is 2.71 kg (CI [2.71, -0.8], p-value = 0.15, so we
fail to reject the null for Fertilizer B vs. Control.

::::

::::spoiler

### Post-hoc: Bonferroni korrektion

EJ KORREKTURLÆST


#### Used for
- Adjusting p-values when performing multiple hypothesis tests to control the family-wise error rate.  
- **Real-world example:** Comparing mean blood pressure between four different diets with all six pairwise t-tests, using Bonferroni to adjust for multiple comparisons.

#### Assumptions
- The individual tests (e.g., pairwise t-tests) satisfy their own assumptions (independence, normality, equal variances if applicable).  
- Tests are independent or positively dependent (Bonferroni remains valid under any dependency but can be conservative).

#### Strengths
- Simple to calculate: multiply each p-value by the number of comparisons.  
- Guarantees control of the family-wise error rate at the chosen α level.  
- Applicable to any set of p-values regardless of test type.

#### Weaknesses
- Very conservative when many comparisons are made, reducing power.  
- Can inflate Type II error (miss true effects), especially with large numbers of tests.  
- Does not take into account the magnitude of dependency among tests.

#### Example

##### Hypothesis
- **Null hypotheses (H₀):** For each pair of diets, the mean blood pressure is equal (e.g., μ_A = μ_B, μ_A = μ_C, …).  
- **Alternative hypotheses (H₁):** For at least one pair, the means differ.


``` r
set.seed(2025)
# Simulate blood pressure for four diet groups (n = 15 each):
diet  <- factor(rep(c("A","B","C","D"), each = 15))
bp    <- c(
  rnorm(15, mean = 120, sd = 8),
  rnorm(15, mean = 125, sd = 8),
  rnorm(15, mean = 130, sd = 8),
  rnorm(15, mean = 135, sd = 8)
)

# Perform all pairwise t-tests with Bonferroni adjustment:
pairwise_result <- pairwise.t.test(bp, diet, p.adjust.method = "bonferroni")

# Display results:
pairwise_result
```

``` output

	Pairwise comparisons using t tests with pooled SD 

data:  bp and diet 

  A       B      C     
B 0.4313  -      -     
C 0.0432  1.0000 -     
D 1.7e-05 0.0083 0.1154

P value adjustment method: bonferroni 
```

Interpretation:
The output shows adjusted p-values for each pair of diets. For example, if the A vs D comparison has p adj = 0.004 (< 0.05), we reject H₀ for that pair and conclude a significant mean difference. Comparisons with p adj ≥ 0.05 fail to reject H₀, indicating no evidence of difference after correction.

::::



## Ikke-parametriske k-prøve-tests


::::spoiler


### Kruskal–Wallis test

EJ KORREKTURLÆST

#### Used for
- Comparing the central tendency of three or more independent groups when the outcome is ordinal or not normally distributed.  
- **Real-world example:** Testing whether median pain scores differ across four treatment groups in a clinical trial when scores are skewed.

#### Assumptions
- Observations are independent both within and between groups.  
- The response variable is at least ordinal.  
- The distributions of the groups have the same shape (so differences reflect shifts in location).

#### Strengths
- Nonparametric: does not require normality or equal variances.  
- Handles ordinal data and skewed continuous data.  
- Controls Type I error when comparing multiple groups without assuming normality.

#### Weaknesses
- Less powerful than one-way ANOVA when normality holds.  
- If group distributions differ in shape, interpretation of a location shift is ambiguous.  
- Only indicates that at least one group differs—post-hoc tests needed to identify which.

#### Example

##### Hypothesis
- **Null hypothesis (H₀):** The distributions (medians) of the four treatment groups are equal.  
- **Alternative hypothesis (H₁):** At least one group’s median pain score differs.


``` r
# Simulate pain scores (0–10) for four treatment groups (n = 15 each):
set.seed(2025)
group <- factor(rep(c("Placebo","DrugA","DrugB","DrugC"), each = 15))
scores <- c(
  rpois(15, lambda = 5),   # Placebo
  rpois(15, lambda = 4),   # Drug A
  rpois(15, lambda = 3),   # Drug B
  rpois(15, lambda = 2)    # Drug C
)
df <- data.frame(group, scores)

# Perform Kruskal–Wallis rank‐sum test:
kw_result <- kruskal.test(scores ~ group, data = df)

# Display results:
kw_result
```

``` output

	Kruskal-Wallis rank sum test

data:  scores by group
Kruskal-Wallis chi-squared = 21.621, df = 3, p-value = 7.82e-05
```

Interpretation:
The Kruskal–Wallis chi-squared = 21.62 with df = 3 and p-value = 7.82\times 10^{-5}. We
reject the null hypothesis.
Thus, there is
evidence that at least one treatment group’s median pain score differs.


::::

::::spoiler


### Rank correlation

EJ KORREKTURLÆST HAV SÆRLIGT FOKUS PÅ OM DER ER FORSKEL PÅ DENNE OG
SPEARMAN-RANK CORRELATION SENERE

#### Used for
- Assessing the strength and direction of a monotonic association between two variables using their ranks.  
- **Real-world example:** Evaluating whether patients’ pain rankings correlate with their anxiety rankings.

#### Assumptions
- Observations are independent.  
- Variables are at least ordinal.  
- The relationship is monotonic (but not necessarily linear).

#### Strengths
- Nonparametric: does not require normality.  
- Robust to outliers in the original measurements.  
- Captures any monotonic relationship, not limited to linear.

#### Weaknesses
- Less powerful than Pearson’s correlation when data are bivariate normal and relationship is linear.  
- Does not distinguish between different monotonic shapes (e.g., concave vs. convex).  
- Ties reduce the effective sample size and complicate exact p-value calculation.

#### Example

##### Hypothesis
- **Null hypothesis (H₀):** There is no monotonic association between X and Y (ρ = 0).  
- **Alternative hypothesis (H₁):** There is a nonzero monotonic association (ρ ≠ 0).


``` r
# Simulate two variables with a monotonic relationship:
set.seed(2025)
x <- sample(1:100, 30)
y <- x + rnorm(30, sd = 10)               # roughly increasing with x
# Perform Spearman rank correlation test:
spearman_result <- cor.test(x, y, method = "spearman", exact = FALSE)
# Display results:
spearman_result
```

``` output

	Spearman's rank correlation rho

data:  x and y
S = 300, p-value = 5.648e-14
alternative hypothesis: true rho is not equal to 0
sample estimates:
      rho 
0.9332592 
```

Interpretation:
Spearman’s ρ = 0.933 with p-value = 5.65\times 10^{-14}. We
reject the null hypothesis.
Thus, there is
evidence of a monotonic association between X and Y.

::::

::::spoiler


### Anderson–Darling test

EJ KORREKTURLÆST

#### Used for
- Testing whether a sample comes from a specified continuous distribution (most commonly normal).  
- **Real-world example:** Checking if daily measurement errors from a laboratory instrument follow a normal distribution.

#### Assumptions
- Observations are independent.  
- Data are continuous (no excessive ties).  
- For goodness‐of‐fit to a non‐normal distribution (e.g. exponential), the distribution’s parameters must be fully specified a priori.

#### Strengths
- More sensitive than the Shapiro–Wilk test to departures in the tails of the distribution.  
- Applicable to a wide range of target distributions (with the appropriate implementation).  
- Provides both a test statistic (A²) and p-value.

#### Weaknesses
- Very sensitive in large samples—small deviations can yield significant results.  
- If parameters are estimated from the data (e.g. normal mean/SD), p-values may be conservative.  
- Does not indicate the form of the departure (e.g. skew vs. kurtosis).

#### Example

##### Hypothesis
- **Null hypothesis (H₀):** The sample is drawn from a Normal distribution.  
- **Alternative hypothesis (H₁):** The sample is not drawn from a Normal distribution.


``` r
# Install and load nortest if necessary:
# install.packages("nortest")
library(nortest)

# Simulate a sample of 40 observations:
set.seed(123)
sample_data <- rnorm(40, mean = 100, sd = 15)

# Perform Anderson–Darling test for normality:
ad_result <- ad.test(sample_data)

# Display results:
ad_result
```

``` output

	Anderson-Darling normality test

data:  sample_data
A = 0.13614, p-value = 0.9754
```

Interpretation:
The Anderson–Darling statistic A² = 0.136 with p-value = 0.975. We
fail to reject the null hypothesis.
Thus, there is
no evidence to conclude a departure from normality.


::::




## Regression og korrelation



::::spoiler

### Simple linear regression

EJ KORREKTURLÆST

#### Used for
- Modeling and quantifying the linear relationship between a continuous predictor and a continuous outcome.  
- **Real-world example:** Predicting house sale price based on living area in square feet.

#### Assumptions
- A linear relationship between predictor and outcome.  
- Residuals are independent and normally distributed with mean zero.  
- Homoscedasticity: constant variance of residuals across values of the predictor.  
- No influential outliers or high-leverage points.

#### Strengths
- Provides an interpretable estimate of the change in outcome per unit change in predictor.  
- Inference on slope and intercept via hypothesis tests and confidence intervals.  
- Basis for more complex regression models and diagnostics.

#### Weaknesses
- Only captures linear patterns; will miss nonlinear relationships.  
- Sensitive to outliers, which can distort estimates and inference.  
- Extrapolation beyond observed predictor range is unreliable.

#### Example

##### Hypothesis
- **Null hypothesis (H₀):** The slope β₁ = 0 (no linear association between x and y).  
- **Alternative hypothesis (H₁):** β₁ ≠ 0 (a linear association exists).


``` r
set.seed(2025)
# Simulate data:
n <- 50
x <- runif(n, min = 0, max = 10)
y <- 2 + 1.5 * x + rnorm(n, sd = 2)
df <- data.frame(x, y)

# Fit simple linear regression:
model <- lm(y ~ x, data = df)

# Show summary of model:
summary(model)
```

``` output

Call:
lm(formula = y ~ x, data = df)

Residuals:
    Min      1Q  Median      3Q     Max 
-4.6258 -1.4244 -0.0462  1.6635  3.6410 

Coefficients:
            Estimate Std. Error t value Pr(>|t|)    
(Intercept)   1.5009     0.6267   2.395   0.0206 *  
x             1.5554     0.1023  15.210   <2e-16 ***
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

Residual standard error: 2.057 on 48 degrees of freedom
Multiple R-squared:  0.8282,	Adjusted R-squared:  0.8246 
F-statistic: 231.3 on 1 and 48 DF,  p-value: < 2.2e-16
```

``` r
# Plot data with regression line:
library(ggplot2)
ggplot(df, aes(x = x, y = y)) +
  geom_point() +
  geom_smooth(method = "lm", se = FALSE) +
  labs(title = "Simple Linear Regression of y on x",
       x = "Predictor (x)",
       y = "Outcome (y)")
```

``` output
`geom_smooth()` using formula = 'y ~ x'
```

<img src="fig/stat-tests-1-rendered-simple_linear_regression-1.png" style="display: block; margin: auto;" />

Interpretation:
The estimated slope is 1.555, with a p-value of 5.51\times 10^{-20}. We
reject the null hypothesis,
indicating that there is
evidence of a significant linear association between x and y.

::::

::::spoiler

### Multiple regression

EJ KORREKTURLÆST

#### Used for
- Modeling the relationship between one continuous outcome and two or more predictors (continuous or categorical).  
- **Real-world example:** Predicting house sale price based on living area, number of bedrooms, and neighborhood quality.

#### Assumptions
- Correct specification: linear relationship between each predictor and the outcome (additivity).  
- Residuals are independent and normally distributed with mean zero.  
- Homoscedasticity: constant variance of residuals for all predictor values.  
- No perfect multicollinearity among predictors.  
- No influential outliers unduly affecting the model.

#### Strengths
- Can adjust for multiple confounders or risk factors simultaneously.  
- Provides estimates and inference (CI, p-values) for each predictor’s unique association with the outcome.  
- Basis for variable selection, prediction, and causal modeling in observational data.

#### Weaknesses
- Sensitive to multicollinearity, which inflates variances of coefficient estimates.  
- Assumes a linear, additive form; interactions or nonlinearity require extension.  
- Outliers and high-leverage points can distort estimates and inference.  
- Interpretation can be complex when including many predictors or interactions.

#### Example

##### Hypothesis
- **Null hypothesis (H₀):** All regression coefficients for predictors (β₁, β₂, β₃) are zero (no association).  
- **Alternative hypothesis (H₁):** At least one βᵢ ≠ 0.


``` r
set.seed(2025)
n <- 100
# Simulate predictors:
living_area     <- runif(n, 800, 3500)        # in square feet
bedrooms        <- sample(2:6, n, replace = TRUE)
neighborhood    <- factor(sample(c("Low","Medium","High"), n, replace = TRUE))
# Simulate price with true model:
price <- 50000 +
         30 * living_area + 
         10000 * bedrooms + 
         ifelse(neighborhood=="Medium", 20000, 
                ifelse(neighborhood=="High", 50000, 0)) +
         rnorm(n, sd = 30000)
df <- data.frame(price, living_area, bedrooms, neighborhood)

# Fit multiple linear regression:
model_mlr <- lm(price ~ living_area + bedrooms + neighborhood, data = df)

# Show model summary:
summary(model_mlr)
```

``` output

Call:
lm(formula = price ~ living_area + bedrooms + neighborhood, data = df)

Residuals:
   Min     1Q Median     3Q    Max 
-76016 -17437   1258  20097  72437 

Coefficients:
                     Estimate Std. Error t value Pr(>|t|)    
(Intercept)        119719.729  12445.109   9.620 1.07e-15 ***
living_area            26.498      3.685   7.190 1.47e-10 ***
bedrooms             8292.136   2147.637   3.861 0.000206 ***
neighborhoodLow    -59241.209   7084.797  -8.362 5.18e-13 ***
neighborhoodMedium -34072.271   6927.394  -4.918 3.65e-06 ***
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

Residual standard error: 28490 on 95 degrees of freedom
Multiple R-squared:  0.5891,	Adjusted R-squared:  0.5718 
F-statistic: 34.05 on 4 and 95 DF,  p-value: < 2.2e-16
```

Interpretation:

The overall F-test (in 34.05 on df₁ = 4, df₂ = 95 has p-value = 1.3\times 10^{-17}, so
reject the null hypothesis.

Individual coefficients: for example, living_area’s estimate is 26.5 (p = 1.47\times 10^{-10}), indicating
a significant positive association: each additional square foot increases price by about \$30 on average.

Similar interpretation applies to bedrooms and neighborhood indicators.






::::

::::spoiler


### Pearson correlation

EJ KORREKTURLÆST

#### Used for
- Assessing the strength and direction of a linear relationship between two continuous variables.  
- **Real-world example:** Examining whether students’ hours of study correlate with their exam scores.

#### Assumptions
- Observations are independent pairs.  
- Both variables are approximately normally distributed (bivariate normality).  
- Relationship is linear.  
- No extreme outliers.

#### Strengths
- Provides both a correlation coefficient (r) and hypothesis test (t‐statistic, p‐value).  
- Confidence interval for the true correlation can be obtained.  
- Well understood and widely used.

#### Weaknesses
- Sensitive to outliers, which can distort r.  
- Only measures linear association—will miss non‐linear relationships.  
- Reliant on normality; departures can affect Type I/II error rates.

#### Example

##### Hypothesis
- **Null hypothesis (H₀):** The true Pearson correlation ρ = 0 (no linear association).  
- **Alternative hypothesis (H₁):** ρ ≠ 0 (a linear association exists).


``` r
set.seed(2025)
# Simulate data:
n <- 30
hours_studied <- runif(n, min = 0, max = 20)
# Make exam_scores roughly increase with hours_studied + noise:
exam_scores   <- 50 + 2.5 * hours_studied + rnorm(n, sd = 5)

# Perform Pearson correlation test:
pearson_result <- cor.test(hours_studied, exam_scores, method = "pearson")

# Display results:
pearson_result
```

``` output

	Pearson's product-moment correlation

data:  hours_studied and exam_scores
t = 14.086, df = 28, p-value = 3.108e-14
alternative hypothesis: true correlation is not equal to 0
95 percent confidence interval:
 0.8689058 0.9694448
sample estimates:
      cor 
0.9361291 
```

Interpretation:
The sample Pearson correlation is 0.936, with t = 14.09 on df = 28 and p-value = 3.11\times 10^{-14}. We
reject the null hypothesis,
indicating that there is
evidence of a significant linear association between hours studied and exam scores.


::::

::::spoiler


### Spearman’s rank correlation

EJ KORREKTURLÆST
#### Used for
- Assessing the strength and direction of a monotonic association between two variables using their ranks.  
- **Real-world example:** Evaluating whether patients’ pain rankings correlate with their anxiety rankings.

#### Assumptions
- Observations are independent pairs.  
- Variables are at least ordinal.  
- The relationship is monotonic (consistently increasing or decreasing).

#### Strengths
- Nonparametric: does not require normality of the underlying data.  
- Robust to outliers in the original measurements.  
- Captures any monotonic relationship, not limited to linear.

#### Weaknesses
- Less powerful than Pearson’s correlation when the true relationship is linear and data are bivariate normal.  
- Does not distinguish between different monotonic shapes (e.g. concave vs. convex).  
- Tied ranks reduce effective sample size and can complicate exact p-value calculation.

#### Example

##### Hypothesis
- **Null hypothesis (H₀):** The true Spearman rank correlation ρ = 0 (no monotonic association).  
- **Alternative hypothesis (H₁):** ρ ≠ 0 (a monotonic association exists).


``` r
# Simulate two variables with a monotonic relationship:
set.seed(2025)
x <- sample(1:100, 30)                   # e.g., anxiety scores ranked
y <- x + rnorm(30, sd = 15)              # roughly increasing with x, plus noise

# Perform Spearman rank correlation test:
spearman_result <- cor.test(x, y, method = "spearman", exact = FALSE)

# Display results:
spearman_result
```

``` output

	Spearman's rank correlation rho

data:  x and y
S = 486, p-value = 3.73e-11
alternative hypothesis: true rho is not equal to 0
sample estimates:
      rho 
0.8918799 
```

Interpretation:
Spearman’s rho = 0.892 with p-value = 3.73\times 10^{-11}. We
reject the null hypothesis.
This indicates that there is
evidence of a significant monotonic association between the two variables.

::::

::::spoiler


### Kendall’s tau

EJ KORREKTURLÆST

#### Used for
- Assessing the strength and direction of a monotonic association between two variables based on concordant and discordant pairs.  
- **Real-world example:** Evaluating whether the ranking of students by homework completion correlates with their ranking by final exam performance.

#### Assumptions
- Observations are independent pairs.  
- Variables are measured on at least an ordinal scale.  
- The relationship is monotonic (but not necessarily linear).

#### Strengths
- Nonparametric: does not require any distributional assumptions.  
- Robust to outliers and tied values (with appropriate corrections).  
- Directly interprets probability of concordance vs. discordance.

#### Weaknesses
- Less powerful than Spearman’s rho when the relationship is strictly monotonic and no ties.  
- Tied ranks reduce effective sample size and require tie corrections.  
- Only measures monotonic association, not form or magnitude of change.

#### Example

##### Hypothesis
- **Null hypothesis (H₀):** Kendall’s τ = 0 (no association between the two rankings).  
- **Alternative hypothesis (H₁):** τ ≠ 0 (a monotonic association exists).


``` r
# Simulate two sets of rankings for 25 students:
set.seed(2025)
homework_rank <- sample(1:25, 25)                  # ranking by homework completion
exam_rank     <- homework_rank + rpois(25, lambda=2) - 1  # roughly related with some noise

# Perform Kendall’s tau test:
kendall_result <- cor.test(homework_rank, exam_rank,
                          method  = "kendall",
                          exact   = FALSE)

# Display results:
kendall_result
```

``` output

	Kendall's rank correlation tau

data:  homework_rank and exam_rank
z = 6.3518, p-value = 2.129e-10
alternative hypothesis: true tau is not equal to 0
sample estimates:
      tau 
0.9203643 
```

Interpretation:
Kendall’s τ = 0.92 with a two-sided p-value = 2.13\times 10^{-10}. We
reject the null hypothesis.
Thus, there is
evidence of a significant monotonic association between homework and exam rankings.

::::

::::spoiler


### Multiple logistic regression

EJ KORREKTURLÆST


#### Used for
- Modeling the probability of a binary outcome as a function of two or more predictors.  
- **Real-world example:** Predicting whether a patient has heart disease (yes/no) based on age, cholesterol level, and smoking status.

#### Assumptions
- Outcome is binary (0/1) and observations are independent.  
- Log-odds of the outcome are a linear function of the predictors (linearity in the logit).  
- No perfect multicollinearity among predictors.  
- Large enough sample so that maximum likelihood estimates are stable (rule of thumb: ≥10 events per predictor).

#### Strengths
- Adjusts for multiple confounders simultaneously.  
- Coefficients have clear interpretation as log‐odds (or odds ratios).  
- Flexible: handles continuous, categorical, and interaction terms.

#### Weaknesses
- Sensitive to complete or quasi‐complete separation (can prevent convergence).  
- Assumes linearity in the logit—requires transformation or splines if violated.  
- Interpretation of interactions and higher‐order terms can be complex.  
- Requires adequate sample size, especially when events are rare.

#### Example

##### Hypothesis
- **Null hypothesis (H₀):** All predictor coefficients β₁ = β₂ = β₃ = 0 (none of the variables affect disease odds).  
- **Alternative hypothesis (H₁):** At least one βᵢ ≠ 0 (at least one predictor affects odds).


``` r
set.seed(2025)
n <- 200
# Simulate predictors:
age          <- rnorm(n, mean = 60, sd = 10)
cholesterol  <- rnorm(n, mean = 200, sd = 30)
smoker       <- factor(rbinom(n, 1, 0.3), labels = c("No", "Yes"))

# Simulate binary outcome via logistic model:
logit_p <- -5 + 0.04 * age + 0.01 * cholesterol + 1.2 * (smoker=="Yes")
p        <- 1 / (1 + exp(-logit_p))
disease  <- rbinom(n, 1, p)

df <- data.frame(disease = factor(disease, labels = c("No","Yes")),
                 age, cholesterol, smoker)

# Fit multiple logistic regression:
model <- glm(disease ~ age + cholesterol + smoker,
             data = df,
             family = binomial)

# Show summary and odds ratios:
summary(model)
```

``` output

Call:
glm(formula = disease ~ age + cholesterol + smoker, family = binomial, 
    data = df)

Coefficients:
             Estimate Std. Error z value Pr(>|z|)    
(Intercept) -7.016100   1.712922  -4.096 4.20e-05 ***
age          0.075418   0.017734   4.253 2.11e-05 ***
cholesterol  0.008465   0.005440   1.556  0.11971    
smokerYes    0.998927   0.360721   2.769  0.00562 ** 
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

(Dispersion parameter for binomial family taken to be 1)

    Null deviance: 265.63  on 199  degrees of freedom
Residual deviance: 237.72  on 196  degrees of freedom
AIC: 245.72

Number of Fisher Scoring iterations: 3
```

``` r
exp(coef(model))
```

``` output
(Intercept)         age cholesterol   smokerYes 
0.000897318 1.078334765 1.008500725 2.715367476 
```

``` r
exp(confint(model))
```

``` output
Waiting for profiling to be done...
```

``` output
                   2.5 %     97.5 %
(Intercept) 2.650867e-05 0.02260073
age         1.042852e+00 1.11825331
cholesterol 9.979236e-01 1.01955252
smokerYes   1.347682e+00 5.57661867
```

Interpretation:

The Wald test for each coefficient (from summary(model)) gives a z-statistic and p-value. For example, if the coefficient for age is β̂ = 0.04 (p = 0.01), its odds ratio is exp(0.04) ≈ 1.04 (95% CI from exp(confint(model))), meaning each additional year of age multiplies the odds of disease by about 1.04.

A significant p-value (e.g., p < 0.05) for cholesterol indicates that higher cholesterol is associated with increased odds (OR = exp(β̂_cholesterol)).

A significant positive coefficient for smoker (β̂ ≈ 1.2, OR ≈ 3.3) implies smokers have about 3.3 times the odds of disease compared to non-smokers, adjusting for age and cholesterol.

You would reject the null hypothesis overall, concluding that at least one predictor is significantly associated with the outcome.









::::

::::spoiler


### Poisson regression

EJ KORREKTURLÆST

#### Used for
- Modeling count data (events per unit time or space) as a function of one or more predictors.  
- **Real-world example:** Predicting the number of daily emergency room visits based on average daily temperature.

#### Assumptions
- Counts follow a Poisson distribution (mean = variance).  
- Events occur independently.  
- The log of the expected count is a linear function of the predictors.  
- No excessive zero‐inflation (if present, consider zero‐inflated models).

#### Strengths
- Naturally handles non‐negative integer outcomes.  
- Estimates incidence rate ratios (IRRs) that are easy to interpret.  
- Can include both categorical and continuous predictors.

#### Weaknesses
- Sensitive to overdispersion (variance > mean); may need quasi‐Poisson or negative binomial.  
- Assumes log‐linear relationship—misspecification leads to bias.  
- Influential observations (e.g., days with extreme counts) can distort estimates.

#### Example

##### Hypothesis
- **Null hypothesis (H₀):** Temperature has no effect on the expected number of ER visits (β₁ = 0).  
- **Alternative hypothesis (H₁):** Temperature affects the expected number of ER visits (β₁ ≠ 0).


``` r
set.seed(2025)
# Simulate 100 days of data:
n_days      <- 100
temp        <- runif(n_days, min = 0,  max = 30)              # average daily temperature (°C)
# True model: log(rate) = 1 + 0.05 * temp
log_rate    <- 1 + 0.05 * temp
expected    <- exp(log_rate)
er_visits   <- rpois(n_days, lambda = expected)              # simulated counts

df <- data.frame(er_visits, temp)

# Fit Poisson regression:
pois_fit <- glm(er_visits ~ temp, family = poisson(link = "log"), data = df)

# Display summary:
summary(pois_fit)
```

``` output

Call:
glm(formula = er_visits ~ temp, family = poisson(link = "log"), 
    data = df)

Coefficients:
            Estimate Std. Error z value Pr(>|z|)    
(Intercept) 0.982531   0.101027   9.725   <2e-16 ***
temp        0.052793   0.004823  10.945   <2e-16 ***
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

(Dispersion parameter for poisson family taken to be 1)

    Null deviance: 233.15  on 99  degrees of freedom
Residual deviance: 104.41  on 98  degrees of freedom
AIC: 463.81

Number of Fisher Scoring iterations: 4
```

Interpretation:
The estimated coefficient for temperature is 0.053, giving an incidence rate ratio IRR = 1.054. With a p-value = 7.01\times 10^{-28}, we
reject the null hypothesis.
This means each 1 °C increase in average daily temperature is associated with a multiplicative change of approximately 1.054 in the expected number of ER visits.




::::

::::spoiler


### Negative binomial regression

EJ KORREKTURLÆST

#### Used for
- Modeling overdispersed count data (variance > mean) as a function of one or more predictors.  
- **Real-world example:** Predicting the number of daily asthma attacks per patient based on air pollution level when counts show extra-Poisson variation.

#### Assumptions
- Counts follow a negative binomial distribution (allows variance > mean).  
- Events occur independently.  
- The log of the expected count is a linear function of the predictors.  
- Overdispersion parameter is constant across observations.

#### Strengths
- Handles overdispersion naturally without biasing standard errors.  
- Estimates incidence rate ratios (IRRs) with correct inference.  
- Can include both continuous and categorical predictors.

#### Weaknesses
- Requires estimation of an extra dispersion parameter, which may be unstable in small samples.  
- Sensitive to model misspecification (link function, omitted covariates).  
- Influential observations can still distort estimates if extreme.

#### Example

##### Hypothesis
- **Null hypothesis (H₀):** Air pollution level has no effect on the expected number of asthma attacks (β₁ = 0).  
- **Alternative hypothesis (H₁):** Air pollution level affects the expected number of asthma attacks (β₁ ≠ 0).


``` r
# install.packages("MASS")  # if necessary
library(MASS)

set.seed(2025)
n_patients <- 150
# Simulate predictor: average daily PM2.5 level (µg/m³)
pm25       <- runif(n_patients, min = 5, max = 50)
# True model: log(µ) = 0.5 + 0.04 * pm25, dispersion theta = 2
log_mu     <- 0.5 + 0.04 * pm25
mu         <- exp(log_mu)
attacks    <- rnbinom(n_patients, mu = mu, size = 2)

df_nb <- data.frame(attacks, pm25)

# Fit negative binomial regression:
nb_fit <- glm.nb(attacks ~ pm25, data = df_nb)

# Display summary:
summary(nb_fit)
```

``` output

Call:
glm.nb(formula = attacks ~ pm25, data = df_nb, init.theta = 2.392967844, 
    link = log)

Coefficients:
            Estimate Std. Error z value Pr(>|z|)    
(Intercept) 0.418832   0.170421   2.458    0.014 *  
pm25        0.045102   0.005085   8.869   <2e-16 ***
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

(Dispersion parameter for Negative Binomial(2.393) family taken to be 1)

    Null deviance: 250.51  on 149  degrees of freedom
Residual deviance: 167.61  on 148  degrees of freedom
AIC: 823.46

Number of Fisher Scoring iterations: 1

              Theta:  2.393 
          Std. Err.:  0.410 

 2 x log-likelihood:  -817.455 
```

Interpretation:
The estimated coefficient for PM2.5 is 0.045, giving an incidence rate ratio IRR = 1.046. With p-value = 7.38\times 10^{-19}, we
reject the null hypothesis.
This means each 1 µg/m³ increase in PM2.5 is associated with a multiplicative change of approximately 1.046 in the expected number of asthma attacks, accounting for overdispersion.

::::

::::spoiler


### Ordinal logistic regression

EJ KORREKTURLÆST

#### Used for
- Modeling an ordinal outcome (with more than two ordered categories) as a function of one or more predictors.  
- **Real-world example:** Predicting customer satisfaction levels (Low, Medium, High) based on service wait time and price.

#### Assumptions
- The dependent variable is ordinal with a meaningful order.  
- **Proportional odds (parallel lines):** the effect of each predictor is the same across all thresholds between outcome categories.  
- Observations are independent.  
- No multicollinearity among predictors.

#### Strengths
- Makes efficient use of the ordering information in the outcome.  
- Provides interpretable odds‐ratios for cumulative probabilities.  
- More powerful than nominal models (e.g., multinomial logit) when the order matters.

#### Weaknesses
- Relies on the proportional‐odds assumption; violation can bias estimates.  
- Interpretation can be less intuitive than linear regression.  
- Cannot easily accommodate non‐proportional effects without extension.

#### Example

##### Hypothesis
- **Null hypothesis (H₀):** Waiting time has no effect on the odds of higher satisfaction (\(\beta_{\text{wait}} = 0\)).  
- **Alternative hypothesis (H₁):** Waiting time affects the odds of higher satisfaction (\(\beta_{\text{wait}} \neq 0\)).


``` r
# Load data and package
library(MASS)

set.seed(2025)
n <- 120
# Simulate predictors:
wait_time <- runif(n,  5, 60)      # waiting time in minutes
price     <- runif(n, 10, 100)     # price in dollars

# Simulate an ordinal outcome via latent variable:
latent   <- 2 - 0.03 * wait_time - 0.01 * price + rnorm(n)
# Define thresholds for three satisfaction levels:
# latent ≤ 0: Low; 0 < latent ≤ 1: Medium; latent > 1: High
satisfaction <- cut(latent,
                    breaks = c(-Inf, 0, 1, Inf),
                    labels = c("Low","Medium","High"),
                    ordered_result = TRUE)

df <- data.frame(satisfaction, wait_time, price)

# Fit proportional‐odds (ordinal logistic) model:
model_polr <- polr(satisfaction ~ wait_time + price, data = df, Hess = TRUE)

# Summarize coefficients and compute p‐values:
ctable <- coef(summary(model_polr))
pvals  <- pnorm(abs(ctable[,"t value"]), lower.tail = FALSE) * 2
results <- cbind(ctable, "p value" = round(pvals, 3))

# Display thresholds and predictor effects:
results
```

``` output
                  Value  Std. Error   t value p value
wait_time   -0.04036665 0.011844352 -3.408093   0.001
price       -0.02267858 0.006578292 -3.447488   0.001
Low|Medium  -3.34230915 0.625031561 -5.347425   0.000
Medium|High -1.78229869 0.564771833 -3.155785   0.002
```

Interpretation:

The estimated coefficient for wait_time is -0.04.

The odds‐ratio is 0.96, meaning each additional minute of wait changes the odds of being in a higher satisfaction category by that factor.

A p‐value of 0.001 for wait_time indicates
rejecting H₀: wait time significantly affects satisfaction odds.

Similar interpretation applies to price.

::::

::::spoiler

### Linear mixed-effects modeller (LME)

EJ KORREKTURLÆST
#### Used for
- Modeling continuous outcomes with both fixed effects (predictors of interest) and random effects (to account for grouped or repeated measures).  
- **Real-world example:** Evaluating the effect of a new teaching method on student test scores, while accounting for variability between classrooms and schools.

#### Assumptions
- Linear relationship between predictors and outcome.  
- Residuals are independent and normally distributed with mean zero.  
- Random effects are normally distributed.  
- Homoscedasticity: constant variance of residuals.  
- Random effects structure correctly specified (e.g., intercepts and/or slopes).

#### Strengths
- Accounts for correlation within clusters (e.g., pupils within the same classroom).  
- Can handle unbalanced data and missing observations within clusters.  
- Flexibly models complex hierarchical or longitudinal data structures.

#### Weaknesses
- Model specification (random effects structure) can be challenging.  
- Parameter estimation can be computationally intensive and may fail to converge.  
- Inference (p-values) often relies on approximations or additional packages.

#### Example

##### Hypothesis
- **Null hypothesis (H₀):** The new teaching method has no effect on student test scores (fixed effect β_method = 0).  
- **Alternative hypothesis (H₁):** The new teaching method affects student test scores (β_method ≠ 0).


``` r
# Install/load necessary packages
# install.packages("lme4"); install.packages("lmerTest")
library(lme4)
```

``` output
Loading required package: Matrix
```

``` r
library(lmerTest)
```

``` output

Attaching package: 'lmerTest'
```

``` output
The following object is masked from 'package:lme4':

    lmer
```

``` output
The following object is masked from 'package:stats':

    step
```

``` r
set.seed(2025)
# Simulate data: 100 students in 10 classrooms within 3 schools
n_schools   <-  3
n_classes   <- 10
students_pc <- 10

school      <- factor(rep(1:n_schools, each = n_classes * students_pc))
class       <- factor(rep(1:(n_schools * n_classes), each = students_pc))
method      <- factor(rep(rep(c("Control","NewMethod"), each = students_pc/2), times = n_schools * n_classes))
# True model: intercept = 70, method effect = 5 points, random intercepts for class & school, residual sd = 8
score <- 70 +
         ifelse(method=="NewMethod", 5, 0) +
         rnorm(n_schools * n_classes * students_pc, sd = 8) +
         rep(rnorm(n_schools * n_classes, sd = 4), each = students_pc) +
         rep(rnorm(n_schools,            sd = 6), each = n_classes * students_pc)

df <- data.frame(score, method, class, school)

# Fit linear mixed-effects model with random intercepts for school and class:
lme_fit <- lmer(score ~ method + (1 | school/class), data = df)

# Summarize model (includes p-values via lmerTest):
summary(lme_fit)
```

``` output
Linear mixed model fit by REML. t-tests use Satterthwaite's method [
lmerModLmerTest]
Formula: score ~ method + (1 | school/class)
   Data: df

REML criterion at convergence: 2132.4

Scaled residuals: 
     Min       1Q   Median       3Q      Max 
-2.69138 -0.67244  0.04868  0.66053  2.86344 

Random effects:
 Groups       Name        Variance Std.Dev.
 class:school (Intercept) 20.88    4.570   
 school       (Intercept) 91.42    9.561   
 Residual                 61.31    7.830   
Number of obs: 300, groups:  class:school, 30; school, 3

Fixed effects:
                Estimate Std. Error       df t value Pr(>|t|)    
(Intercept)      64.6076     5.6194   2.0276  11.497  0.00712 ** 
methodNewMethod   5.9555     0.9042 268.9993   6.587 2.35e-10 ***
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

Correlation of Fixed Effects:
            (Intr)
methdNwMthd -0.080
```

Interpretation:
The fixed-effect estimate for NewMethod is 5.96 points (SE = 0.9), with p = 2.35\times 10^{-10}. We
reject the null hypothesis.
Thus, there is
evidence that the new teaching method significantly changes test scores, accounting for classroom and school variability.


::::

::::spoiler

### Generalized linear mixed-effects modeller (GLMM)

EJ KORREKTURLÆST

#### Used for
- Modeling non-normal outcomes (e.g. binary, counts) with both fixed effects and random effects.  
- **Real-world example:** Predicting whether patients are readmitted (yes/no) based on age and comorbidity score, with random intercepts for each hospital.

#### Assumptions
- Observations within each cluster (e.g. hospital) are correlated, but clusters are independent.  
- The conditional distribution of the outcome given predictors and random effects follows a specified exponential‐family distribution (e.g. binomial, Poisson).  
- The link function (e.g. logit, log) correctly relates the linear predictor to the mean of the outcome.  
- Random effects are normally distributed.

#### Strengths
- Can accommodate hierarchical or longitudinal data and non-Gaussian outcomes.  
- Estimates both population‐level (fixed) effects and cluster‐specific (random) variation.  
- Flexible: supports a variety of link functions and distributions.

#### Weaknesses
- Computationally intensive; convergence can fail with complex random‐effects structures.  
- Inference (especially p-values for fixed effects) relies on approximations.  
- Model specification (random slopes, link choice) can be challenging.

#### Example

##### Hypothesis
- **Null hypothesis (H₀):** The log-odds of readmission do not depend on age (β_age = 0).  
- **Alternative hypothesis (H₁):** Age affects the log-odds of readmission (β_age ≠ 0).


``` r
# Load packages
library(lme4)

set.seed(2025)
# Simulate data for 2000 patients in 20 hospitals:
n_hospitals <- 20
patients_per <- 100
hospital   <- factor(rep(1:n_hospitals, each = patients_per))
age        <- rnorm(n_hospitals * patients_per, mean = 65, sd = 10)
comorbidity<- rpois(n_hospitals * patients_per, lambda = 2)

# True model: logit(p) = -2 + 0.03*age + 0.5*comorbidity + random intercept by hospital
logit_p    <- -2 + 0.03 * age + 0.5 * comorbidity + rnorm(n_hospitals, 0, 0.5)[hospital]
p_readmit  <- plogis(logit_p)
readmit    <- rbinom(n_hospitals * patients_per, size = 1, prob = p_readmit)

df_glmm <- data.frame(readmit = factor(readmit, levels = c(0,1)),
                      age, comorbidity, hospital)

# Fit a binomial GLMM with random intercepts for hospital:
glmm_fit <- glmer(readmit ~ age + comorbidity + (1 | hospital),
                  data = df_glmm, family = binomial(link = "logit"))

# Display summary:
summary(glmm_fit)
```

``` output
Generalized linear mixed model fit by maximum likelihood (Laplace
  Approximation) [glmerMod]
 Family: binomial  ( logit )
Formula: readmit ~ age + comorbidity + (1 | hospital)
   Data: df_glmm

      AIC       BIC    logLik -2*log(L)  df.resid 
   2200.4    2222.8   -1096.2    2192.4      1996 

Scaled residuals: 
    Min      1Q  Median      3Q     Max 
-3.9499 -0.9147  0.4555  0.6420  1.7336 

Random effects:
 Groups   Name        Variance Std.Dev.
 hospital (Intercept) 0.1931   0.4395  
Number of obs: 2000, groups:  hospital, 20

Fixed effects:
             Estimate Std. Error z value Pr(>|z|)    
(Intercept) -2.132901   0.370427  -5.758 8.51e-09 ***
age          0.035056   0.005317   6.593 4.30e-11 ***
comorbidity  0.457562   0.044238  10.343  < 2e-16 ***
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

Correlation of Fixed Effects:
            (Intr) age   
age         -0.932       
comorbidity -0.293  0.101
```

Interpretation:
The fixed‐effect estimate for age is 0.035 (SE = 0.005), giving an odds ratio of 1.036 per year of age. With a z‐value = 6.59 and p ≈ 4.3\times 10^{-11}, we
reject the null hypothesis.
This indicates that age
is significantly associated with higher odds of readmission, accounting for hospital clustering.

::::

::::spoiler

### Generalized Estimating Equations (GEE)

EJ KORREKTURLÆST

::::



## Kontingenstabel- og proportions-tests


::::spoiler

### Contingency-table methods (χ² osv.)

EJ KORREKTURLÆST

::::

::::spoiler

### McNemar’s test

EJ KORREKTURLÆST

::::

::::spoiler


### Fisher’s exact test

EJ KORREKTURLÆST
::::

::::spoiler

### Barnard’s exact test

EJ KORREKTURLÆST

::::

::::spoiler


### Cochran–Armitage trend test (ordinal tabel)

EJ KORREKTURLÆST

::::

::::spoiler

### Cochran’s Q test (≥3 matched proportions)

EJ KORREKTURLÆST

::::

::::spoiler


### Stuart–Maxwell test (marginal homogenitet)

EJ KORREKTURLÆST

::::

::::spoiler

### Two-sample test for binomial proportions / Mantel–Haenszel test

EJ KORREKTURLÆST

::::

::::spoiler

### Chi-square test for R×C-tabeller

EJ KORREKTURLÆST

::::

::::spoiler

### Chi-square test for trend (Mantel-extension)

EJ KORREKTURLÆST

::::

::::spoiler

### Chi-square test for heterogenitet (2×k-tabeller)

EJ KORREKTURLÆST

::::



## Incidens- og rate-tests



::::spoiler


### One-sample test for incidence rates

EJ KORREKTURLÆST

#### Used for
- Testing whether an observed incidence rate (events per unit person‐time) differs from a specified rate.  
- **Real-world example:** Determining if a hospital’s rate of catheter‐associated infections (per 1,000 patient‐days) equals the national benchmark.

#### Assumptions
- Events occur independently and follow a Poisson process.  
- The incidence rate is constant over the observation period.  
- Person‐time is measured accurately and non‐overlapping.

#### Strengths
- Exact test based on the Poisson distribution—no large‐sample approximation needed.  
- Naturally accounts for differing follow‐up times via person‐time.  
- Valid for rare events and small counts.

#### Weaknesses
- Sensitive to overdispersion (variance > mean) and violation of Poisson assumptions.  
- Cannot adjust for covariates or time‐varying rates.  
- Assumes homogeneity of the rate across the period.

#### Example

##### Hypothesis
- **Null hypothesis (H₀):** The true incidence rate λ = 2 infections per 1,000 patient‐days.  
- **Alternative hypothesis (H₁):** λ ≠ 2 infections per 1,000 patient‐days.


``` r
# Observed infections and total patient‐days:
events    <- 8
patient_days <- 3500   # total follow‐up time in patient‐days

# Hypothesized rate (infections per 1 patient‐day):
# 2 per 1,000 patient‐days = 0.002 per patient‐day
rate0     <- 2 / 1000  

# Perform one‐sample incidence‐rate test:
test_result <- poisson.test(x = events,
                            T = patient_days,
                            r = rate0,
                            alternative = "two.sided")

# Display results:
test_result
```

``` output

	Exact Poisson test

data:  events time base: patient_days
number of events = 8, time base = 3500, p-value = 0.702
alternative hypothesis: true event rate is not equal to 0.002
95 percent confidence interval:
 0.0009868092 0.0045037683
sample estimates:
 event rate 
0.002285714 
```

Interpretation:
The test compares the observed 8 infections over 3,500 patient‐days to the expected rate of 2/1,000 patient‐days (i.e., 7 infections). With a p‐value of r signif(test_result$p.value, 3), we
r if(test_result$p.value < 0.05) "reject the null hypothesis" else "fail to reject the null hypothesis".
Thus, there is
r if(test_result$p.value < 0.05) "evidence that the hospital’s infection rate differs from 2 per 1,000 patient‐days." else "no evidence that the infection rate differs from the benchmark."

::::

::::spoiler

### Two-sample comparison of incidence rates

EJ KORREKTURLÆST
::::

::::spoiler

### Trend-test for incidence rates over flere eksponeringsgrupper

EJ KORREKTURLÆST
::::

::::spoiler

### Exact rate ratio test

EJ KORREKTURLÆST

::::



## Overlevelsesanalyse



::::spoiler


### Log-rank test

EJ KORREKTURLÆST

::::

::::spoiler


### Parametric survival methods (Weibull)

EJ KORREKTURLÆST

::::

::::spoiler

### Cox proportional hazards model

EJ KORREKTURLÆST

::::

::::spoiler

### Accelerated Failure Time (AFT) modeller (eksponentiel, log-logistisk, …)

EJ KORREKTURLÆST

::::

::::spoiler

### Gray’s test for konkurrentrisiko

EJ KORREKTURLÆST

::::

::::spoiler

### Test af proportional hazards-antagelsen (Schoenfeld residualer)

EJ KORREKTURLÆST
::::

## Aftale- og concordance-mål




::::spoiler
### Kappa statistic

EJ KORREKTURLÆST

::::

::::spoiler

### Intraclass Correlation Coefficient (ICC)

EJ KORREKTURLÆST

#### Used for
- Assessing the reliability or agreement of quantitative measurements made by two or more raters.  
- **Real-world example:** Determining how consistently three radiologists measure tumor size on MRI scans.

#### Assumptions
- Measurements are continuous and approximately normally distributed.  
- Raters are randomly selected (for the “random‐effects” model) or fixed (for the “fixed‐effects” model), depending on choice.  
- No interaction between subjects and raters (i.e., rater effects are consistent across subjects).  
- Balanced design: each subject is rated by the same set of raters.

#### Strengths
- Quantifies both consistency and absolute agreement, with different model/type options.  
- Can accommodate any number of raters and subjects.  
- Provides confidence intervals and tests for ICC.

#### Weaknesses
- Sensitive to violations of normality and homogeneity of variance.  
- Choice of model (one‐way vs. two‐way) and type (consistency vs. agreement) affects results.  
- Requires balanced data; missing ratings complicate estimation.

#### Example

##### Hypothesis
- **Null hypothesis (H₀):** The intraclass correlation coefficient ICC = 0 (no reliability beyond chance).  
- **Alternative hypothesis (H₁):** ICC > 0 (measurements are more reliable than chance).


``` r
library(irr)
```

``` output
Loading required package: lpSolve
```

``` r
# Simulate ratings of 10 subjects by 3 raters:
set.seed(42)
ratings <- data.frame(
  rater1 = round(rnorm(10, mean = 50, sd = 5)),
  rater2 = round(rnorm(10, mean = 50, sd = 5)),
  rater3 = round(rnorm(10, mean = 50, sd = 5))
)

# Compute two-way random effects, absolute agreement, single rater ICC:
icc_result <- icc(ratings,
                  model = "twoway",
                  type  = "agreement",
                  unit  = "single")

# Display results:
icc_result
```

``` output
 Single Score Intraclass Correlation

   Model: twoway 
   Type : agreement 

   Subjects = 10 
     Raters = 3 
   ICC(A,1) = -0.0823

 F-Test, H0: r0 = 0 ; H1: r0 > 0 
  F(9,16.8) = 0.77 , p = 0.645 

 95%-Confidence Interval for ICC Population Values:
  -0.326 < ICC < 0.382
```
Interpretation:
The estimated ICC is -0.08 with a 95% CI [-0.33, 0.38] and p-value = 0.645. We
fail to reject the null hypothesis.
This indicates that
there is no evidence of reliability beyond chance among the raters.


::::

::::spoiler
### Bland–Altman analysis

EJ KORREKTURLÆST

#### Used for
Assessing agreement between two quantitative measurement methods by examining 
the mean difference (bias) and the limits of agreement. It tests if any difference
is constant across the range of measurements, and if there is heteroskedasticity
in the data (are there differences that are dependent on measurement levels)
**Real-world example:** Comparing blood pressure readings from a new wrist monitor and a standard sphygmomanometer.


#### Assumptions
- Paired measurements on the same subjects.  
- Differences (method A – method B) are approximately normally distributed.  
- No strong relationship between the magnitude of the measurement and the difference (homoscedasticity).

#### Strengths
- Provides both a visual (Bland–Altman plot) and numerical summary (bias and limits) of agreement.  
- Easy to interpret clinically: shows how far apart two methods can differ for most observations.  
- Does not rely on correlation, which can be misleading for agreement.

#### Weaknesses
- Assumes constant bias across range of measurements.  
- Sensitive to outliers, which can widen limits of agreement.  
- Requires adequate sample size (n ≥ 30 preferred) to estimate limits reliably.

#### Example

##### Hypothesis
- **Null hypothesis (H₀):** Mean difference between methods A and B is zero (no systematic bias).  
- **Alternative hypothesis (H₁):** Mean difference ≠ 0 (systematic bias exists).


``` r
# Simulated paired blood pressure measurements (mmHg) on 12 subjects:
wrist   <- c(120, 122, 118, 121, 119, 117, 123, 120, 118, 119, 122, 121)
sphyg   <- c(119, 121, 117, 122, 118, 116, 124, 119, 117, 120, 121, 122)

# Compute differences and means:
diffs    <- wrist - sphyg
means    <- (wrist + sphyg) / 2

# Calculate bias and limits of agreement:
bias     <- mean(diffs)
sd_diff  <- sd(diffs)
loa_up   <- bias + 1.96 * sd_diff
loa_low  <- bias - 1.96 * sd_diff

# Test for zero bias:
t_test   <- t.test(diffs, mu = 0)

# Bland–Altman plot:
library(ggplot2)
ba_df <- data.frame(mean = means, diff = diffs)
ggplot(ba_df, aes(x = mean, y = diff)) +
  geom_point() +
  geom_hline(yintercept = bias,    linetype = "solid") +
  geom_hline(yintercept = loa_up,   linetype = "dashed") +
  geom_hline(yintercept = loa_low,  linetype = "dashed") +
  labs(title = "Bland–Altman Plot",
       x = "Mean of Wrist & Sphyg Measurements",
       y = "Difference (Wrist – Sphyg)")
```

<img src="fig/stat-tests-1-rendered-bland_altman-1.png" style="display: block; margin: auto;" />

``` r
# Print numerical results:
bias; loa_low; loa_up; t_test
```

``` output
[1] 0.3333333
```

``` output
[1] -1.596741
```

``` output
[1] 2.263408
```

``` output

	One Sample t-test

data:  diffs
t = 1.1726, df = 11, p-value = 0.2657
alternative hypothesis: true mean is not equal to 0
95 percent confidence interval:
 -0.2923355  0.9590022
sample estimates:
mean of x 
0.3333333 
```

The mean difference (bias) is 0.33 units, with 95% limits of 
agreement from -1.6 to 2.26 units. 
The t-test for zero bias yields a p-value of 0.266, so we
fail to reject the null hypothesis.
This indicates that there is
no statistically significant bias; the two methods agree on average.

::::


::::::::::::::::::::::::::::::::::::: keypoints 

- Use `.md` files for episodes when you want static content


::::::::::::::::::::::::::::::::::::::::::::::::




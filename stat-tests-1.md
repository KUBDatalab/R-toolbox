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

::::

::::spoiler

### Two-sample t test (unequal variances)

EJ KORREKTURLÆST

::::

::::spoiler

### Mann–Whitney U-test (Wilcoxon rank-sum)

EJ KORREKTURLÆST

::::

::::spoiler

### Wilcoxon signed-rank test

EJ KORREKTURLÆST

::::

::::spoiler

### Kolmogorov–Smirnov to-prøve-test

EJ KORREKTURLÆST


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

::::



## Variansanalyse (ANOVA/ANCOVA)

::::spoiler
### One-way ANOVA

EJ KORREKTURLÆST


::::

::::spoiler
### One-way ANCOVA

EJ KORREKTURLÆST


::::

::::spoiler
### Welch’s ANOVA (uden antagelse om lige varianser)

EJ KORREKTURLÆST

::::

::::spoiler

### Repeated-measures ANOVA

EJ KORREKTURLÆST

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

::::

::::spoiler

### Post-hoc: Tukey HSD

EJ KORREKTURLÆST

::::

::::spoiler

### Post-hoc: Dunnett’s test

EJ KORREKTURLÆST

::::

::::spoiler

### Post-hoc: Bonferroni korrektion

EJ KORREKTURLÆST


::::



## Ikke-parametriske k-prøve-tests


::::spoiler


### Kruskal–Wallis test

EJ KORREKTURLÆST

::::

::::spoiler


### Rank correlation

EJ KORREKTURLÆST

::::

::::spoiler


### Anderson–Darling test

EJ KORREKTURLÆST

::::




## Regression og korrelation



::::spoiler

### Simple linear regression

EJ KORREKTURLÆST

::::

::::spoiler

### Multiple regression

EJ KORREKTURLÆST

::::

::::spoiler


### Pearson correlation

EJ KORREKTURLÆST

::::

::::spoiler


### Spearman’s rank correlation

EJ KORREKTURLÆST

::::

::::spoiler


### Kendall’s tau

EJ KORREKTURLÆST

::::

::::spoiler


### Multiple logistic regression

EJ KORREKTURLÆST

::::

::::spoiler


### Poisson regression

EJ KORREKTURLÆST

::::

::::spoiler


### Negative binomial regression

EJ KORREKTURLÆST

::::

::::spoiler


### Ordinal logistic regression

EJ KORREKTURLÆST

::::

::::spoiler

### Linear mixed-effects modeller (LME)

EJ KORREKTURLÆST

::::

::::spoiler

### Generalized linear mixed-effects modeller (GLMM)

EJ KORREKTURLÆST

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




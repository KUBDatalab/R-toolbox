---
title: 'Tuning kmeans'
teaching: 10
exercises: 2
---

:::::::::::::::::::::::::::::::::::::: questions 

- What k should I chose in my kmeans clustering?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

- Explain how to tune the hyperparameter k in kmeans


::::::::::::::::::::::::::::::::::::::::::::::::


We know how to run a kmeans clustering on our data.

But one question remains. What k should we chose? 

k-means is an unsupervised clustering technique, but we do need to chose the 
number of cluster we want it to categorise our data in.

We might have domain knowledge informing us that there should be three clusters.

But otherwise we are left to our own devices and have to make a choice. 

We can chose several different k, and look at the results in order to make 
that determination. But we would'nt normally do this kind of thing. It is
much better to have a numerical indication independent of our own prejudices
to make the determination.

We call this Hyperparameter Tuning, and there are automated techniques for this.

:::: callout

### Why hyper?

Normally whe we do machine learning, we do that by adjusting a set of parameters
in order to get the best possible result. k in kmeans however is not at parameter
we learn from the data when we train the model. It is a parameter we chose, 
that is part of the structure of the model rather than something the algorithm 
optimises automatically.

::::

### What is a good clustering?

One intuitive way of looking at the quality of clusters, would be to look at 
the distance between all points and the clusters they have been assigned 
to. A model where all points are close to their centroid, is regarded as a better
model where the points are a longer way from their centroid.

We can do a k-means clustering for several different k, and calculate the 
distance. As we often do when we calculate distances, we square them in order
to handle different directions of the distance. And to penalize models with
very large outliers. This sum is directly available in the kmeans result, with
the name `tot.withinss´

Let us look at an example.

We need to generate some data:


``` r
set.seed(123)
data <- matrix(rnorm(300), ncol = 3)
```

This creates 300 random values, and places them into a matrix with three columns.

Running kmeans directly with eg k = 3, can give us the total:


``` r
kmeans(data, centers = 3)$tot.withinss
```

``` output
[1] 165.6211
```

We could do that by hand for multiple values of k, but write a piece of code for it:


``` r
library(purrr)
wss <- map_dbl(1:10, function(k){
  kmeans(data, centers = k, nstart = 20)$tot.withinss
})
```

We use a slightly advanced technique, where map_dbl is a function that can
run another function on a sequence of input. That other function is keams, with
a variable k, using the `$` notation to extract the sum of the squared distances
from the data points to their cluster centroids.

We can collect that in a dataframe:


``` r
library(tibble)
wss_df <- tibble(k = 1:10,
                 wss = wss)
wss_df
```

``` output
# A tibble: 10 × 2
       k   wss
   <int> <dbl>
 1     1 264. 
 2     2 198. 
 3     3 147. 
 4     4 119. 
 5     5 101. 
 6     6  88.4
 7     7  80.2
 8     8  73.2
 9     9  66.1
10    10  61.7
```

And now we can plot the result:


``` r
library(tidyverse)
```

``` output
── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
✔ dplyr     1.1.4     ✔ readr     2.1.5
✔ forcats   1.0.0     ✔ stringr   1.5.1
✔ ggplot2   3.5.1     ✔ tidyr     1.3.1
✔ lubridate 1.9.4     
── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
✖ dplyr::filter() masks stats::filter()
✖ dplyr::lag()    masks stats::lag()
ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors
```

``` r
wss_df %>%
  ggplot(aes(x = k, y = wss)) +
  geom_point() +
  geom_line() +
  labs(
    x = "k (number of clusters)",
    y = "Total within-cluster sum of squares (WSS)",
    title = "Elbow-method: WSS vs. k"
  ) 
```

<img src="fig/tuning-kmeans-rendered-unnamed-chunk-4-1.png" style="display: block; margin: auto;" />
It is clear that 10 clusters is better than 5. So, should we use 10? No.

We will be able to get the value down to 0 if we just choose enough clusters. 
And nothing new is really learnt if 100 datapoints are assigned to 100 clusters,
one each. The correct choice of k here, is the point where the curve bends. At 
the hook of the elbow. Somewhere around k = 3 or maybe k = 4. If we chose a larger
k, we do get better clustering, but we begin to overfit. The addition of an
extra cluster does not really improve the result.

:::: challenge
## Try it yourself

Read in the wine dataset we have looked at earlier:

``` r
library(readr)
vin <- read_csv("data/win/wine.data", col_names = F)
```

``` output
Rows: 178 Columns: 14
── Column specification ────────────────────────────────────────────────────────
Delimiter: ","
dbl (14): X1, X2, X3, X4, X5, X6, X7, X8, X9, X10, X11, X12, X13, X14

ℹ Use `spec()` to retrieve the full column specification for this data.
ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

``` r
names(vin) <- c("cultivar",
                "Alcohol",
                "Malicacid",
                "Ash",
                "Alcalinityofash",
                "Magnesium",
                "Totalphenols",
                "Flavanoids",
                "Nonflavanoidphenols",
                "Proanthocyanins",
                "Colourintensity",
                "Hue",
                "OD280OD315ofdilutedwines",
                "Proline")
```

What is the optimal k for a kmeans clustering on this dataset? Remember to 
remove the cultivar column. 

:::: solution

``` r
library(dplyr)
vin <- vin %>% select(-cultivar)
wss <- map_dbl(1:10, function(k){
  kmeans(vin, centers = k, nstart = 20)$tot.withinss
})
tibble(k = 1:10,
                 wss = wss) %>%
  ggplot(aes(x = k, y = wss)) +
  geom_point() +
  geom_line()
```

<img src="fig/tuning-kmeans-rendered-unnamed-chunk-6-1.png" style="display: block; margin: auto;" />

::::

::::


library(factoextra)


``` r
# Installer og load nødvendige pakker
# install.packages("cluster")
library(cluster)

set.seed(123)
# Simulerer et datasæt med 100 observationer og 3 variable
data <- matrix(rnorm(300), ncol = 3)

# Vælg et interval for antal klynger (k)
k_values <- 2:10  # Silhouette kræver mindst 2 klynger

# Initialiser en vektor til at gemme gennemsnitlig silhuet-bredde
avg_silhouette <- numeric(length(k_values))

# Beregn k-means clustering og silhuet for hvert k
for(i in seq_along(k_values)) {
  k <- k_values[i]
  km_res <- kmeans(data, centers = k, nstart = 25)
  # Beregn afstanden mellem observationerne
  diss <- dist(data)
  # Beregn silhuet-værdierne for den opnåede clustering
  sil <- silhouette(km_res$cluster, diss)
  # Gem den gennemsnitlige silhuet-bredde
  avg_silhouette[i] <- mean(sil[, 3])
}

# Plot den gennemsnitlige silhuet-bredde for hvert k
plot(k_values, avg_silhouette, type = "b", pch = 19, frame = FALSE,
     xlab = "Antal klynger (k)",
     ylab = "Gennemsnitlig silhuet-bredde",
     main = "Silhouette-metoden til valg af k")
```

<img src="fig/tuning-kmeans-rendered-unnamed-chunk-7-1.png" style="display: block; margin: auto;" />

``` r
# Find det optimale k (hvor gennemsnitlig silhuet er størst)
optimal_k <- k_values[which.max(avg_silhouette)]
cat("Optimalt antal klynger ifølge silhouette-metoden er:", optimal_k, "\n")
```

``` output
Optimalt antal klynger ifølge silhouette-metoden er: 6 
```

``` r
# Installer og load nødvendige pakker
# install.packages("cluster")
library(cluster)

set.seed(123)
# Simuler et datasæt med 100 observationer og 3 variable
data <- matrix(rnorm(300), ncol = 3)

# Beregn gap-statistikken for klynger fra 1 til 10
gap_stat <- clusGap(data,
                    FUN = kmeans,
                    nstart = 25,
                    K.max = 10,
                    B = 50)  # B er antallet af bootstrap-replikationer

# Vis gap-statistikken
print(gap_stat, method = "firstmax")
```

``` output
Clustering Gap statistic ["clusGap"] from call:
clusGap(x = data, FUNcluster = kmeans, K.max = 10, B = 50, nstart = 25)
B=50 simulated reference sets, k = 1..10; spaceH0="scaledPCA"
 --> Number of clusters (method 'firstmax'): 1
          logW   E.logW       gap     SE.sim
 [1,] 3.963826 4.441487 0.4776613 0.02498523
 [2,] 3.807349 4.233483 0.4261336 0.02539433
 [3,] 3.649488 4.085792 0.4363040 0.02790699
 [4,] 3.546001 3.961003 0.4150029 0.03199243
 [5,] 3.460285 3.858558 0.3982727 0.02996649
 [6,] 3.392734 3.767463 0.3747299 0.02782729
 [7,] 3.333614 3.691194 0.3575803 0.02710658
 [8,] 3.278133 3.623151 0.3450172 0.02665343
 [9,] 3.223328 3.563249 0.3399207 0.02649002
[10,] 3.184523 3.509952 0.3254286 0.02753950
```

``` r
# Plot gap-statistikken
plot(gap_stat, main = "Gap-statistik til valg af antal klynger")
```

<img src="fig/tuning-kmeans-rendered-unnamed-chunk-8-1.png" style="display: block; margin: auto;" />


::::::::::::::::::::::::::::::::::::: keypoints 

- Use `.md` files for episodes when you want static content
- Use `.Rmd` files for episodes when you need to generate output
- Run `sandpaper::check_lesson()` to identify any issues with your lesson
- Run `sandpaper::build_lesson()` to preview your lesson locally

::::::::::::::::::::::::::::::::::::::::::::::::


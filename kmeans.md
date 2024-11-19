---
title: 'k-means'
teaching: 10
exercises: 2
---

:::::::::::::::::::::::::::::::::::::: questions 

- What is k-means?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

- Explain how to use markdown with the new lesson template
- Demonstrate how to include pieces of code, figures, and nested challenge blocks

::::::::::::::::::::::::::::::::::::::::::::::::

## What is k-means?

k-means is a clustering algorithm. We have some data, and we
would like to partition that data into a set of distinct, non-overlapping clusters.

And we would like those clusters to be based on the similarity
of the features of the data.

When we talk about features, we talk about variables, or dimensions
in the data.

k-means is an unsupervised machine learning algorithm. We do
not know the "true" groups, but simply want to group the results.

Here we have some data on wine. Different parameters of wine from
three different grapes (or cultivars) have been measured. 
Most of them are chemical measurements.


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
                "Colorintensity",
                "Hue",
                "OD280OD315ofdilutedwines",
                "Proline")
```

Here we have chosen a data set where we know the "true" values
in order to illustrate the process.

## What does it do?

K-means works by chosing K - the number of clusters we want. That is 
a choice entirely up to us. 

The algorithm then choses K random points, also called centroids.
In this case these centroids
are in 13 dimensions, because we have 13 features in the dataset.

It then calculates the distance from each observation in the data,
to each of the K centroids. After that each observation is assigned
to the centroid it is closest to. We are going to set K = 3 
because we know that there are three clusters in the data. Each
point will therefore be assigned to a specific cluster, described
by the relevant centroid. 

The algorithm then calculates three new centroids. All the observations
assigned to centroid A are averaged, and we get a new centroid.
The same calculations are done for the other two centroids, and
we now have three new centroids. They now actually have a relation
to the data - before they we assigned randomly, now they are 
based on the data.

Now the algorithm repeats the calculation of distance. For each
observation in the data, which centroid is it closest to. Since 
we calculated new centroids, some of the observations will switch 
and will be assigned to a new centroid. 

Again the new assignments of observations are used to calculate
new centroids for the three clusters, and all calculations repeated
until no observations swithces between clusters, or we have repeated
the algorithm a set number of times (by default 10 times).

After that, we have clustered our data in three (or whatever K
we chose) clusters, based on the features of the data.

## How do we actually do that?

The algorithm only works with numerical data, and we of course have
to remove the variable containing the "true" clusters.


``` r
cluster_data <- vin %>% 
  select(-cultivar)
```

After that, we run the function `kmeans`, and specify that 
we want three centers (K), and save the result to an object,
that we can work with afterwards:

``` r
clustering <- kmeans(cluster_data, centers = 3)
clustering
```

``` output
K-means clustering with 3 clusters of sizes 69, 47, 62

Cluster means:
   Alcohol Malicacid      Ash Alcalinityofash Magnesium Totalphenols Flavanoids
1 12.51667  2.494203 2.288551        20.82319  92.34783     2.070725   1.758406
2 13.80447  1.883404 2.426170        17.02340 105.51064     2.867234   3.014255
3 12.92984  2.504032 2.408065        19.89032 103.59677     2.111129   1.584032
  Nonflavanoidphenols Proanthocyanins Colorintensity       Hue
1           0.3901449        1.451884       4.086957 0.9411594
2           0.2853191        1.910426       5.702553 1.0782979
3           0.3883871        1.503387       5.650323 0.8839677
  OD280OD315ofdilutedwines   Proline
1                 2.490725  458.2319
2                 3.114043 1195.1489
3                 2.365484  728.3387

Clustering vector:
  [1] 2 2 2 2 3 2 2 2 2 2 2 2 2 2 2 2 2 2 2 3 3 3 2 2 3 3 2 2 3 2 2 2 2 2 2 3 3
 [38] 2 2 3 3 2 2 3 3 2 2 2 2 2 2 2 2 2 2 2 2 2 2 1 3 1 3 1 1 3 1 1 3 3 3 1 1 2
 [75] 3 1 1 1 3 1 1 3 3 1 1 1 1 1 3 3 1 1 1 1 1 3 3 1 3 1 3 1 1 1 3 1 1 1 1 3 1
[112] 1 3 1 1 1 1 1 1 1 3 1 1 1 1 1 1 1 1 1 3 1 1 3 3 3 3 1 1 1 3 3 1 1 3 3 1 3
[149] 3 1 1 1 1 3 3 3 1 3 3 3 1 3 1 3 3 1 3 3 3 3 1 1 3 3 3 3 3 1

Within cluster sum of squares by cluster:
[1]  443166.7 1360950.5  566572.5
 (between_SS / total_SS =  86.5 %)

Available components:

[1] "cluster"      "centers"      "totss"        "withinss"     "tot.withinss"
[6] "betweenss"    "size"         "iter"         "ifault"      
```
We know the true values, so we extract the clusters the algorithm found, and
match them with the true values, and count the matches:


``` r
tibble(quess = clustering$cluster, true = vin$cultivar) %>% 
table()
```

``` output
     true
quess  1  2  3
    1  0 50 19
    2 46  1  0
    3 13 20 29
```

The algorithm have no idea about the numbering, the three groups are numbered 
1 to 3 randomly. It appears that cluster 2 from the algorithm matches cluster 2
from the data. The two other clusters are a bit more confusing.

Does that mean the algorithm does a bad job?


## Preprocessing is necessary!

Looking at the data give a hint about the problems:


``` r
# head(vin)
```
Note the differences between the values of "Malicacid" and "Magnesium". One
have values between 0.74 and 5.8 and the
other bewtten 70 and 162. The latter
influences the means much more that the former. 

It is therefore good practice to scale the features to have the same range and 
standard deviation:


``` r
scaled_data <- scale(cluster_data)
```

If we now do the same clustering as before, but now on the scaled data, we 
get much better results:



``` r
set.seed(42)
clustering <- kmeans(scaled_data, centers = 3)
tibble(quess = clustering$cluster, true = vin$cultivar) %>% 
  table()
```

``` output
     true
quess  1  2  3
    1 59  3  0
    2  0 65  0
    3  0  3 48
```

By chance, the numbering of the clusters now matches the "true" cluster numbers.

The clustering is not perfect. 3 observations belonging to cluster 2, are assigned
to cluster 1 by the algorithm (and 3 more to cluster 3).

## What are those distances?

It is easy to measure the distance between two observations when we only have
two features/dimensions: Plot them on a piece of paper, and bring out the ruler.

But what is the distance between this point:

1, 14.23, 1.71, 2.43, 15.6, 127, 2.8, 3.06, 0.28, 2.29, 5.64, 1.04, 3.92, 1065 and 1, 13.2, 1.78, 2.14, 11.2, 100, 2.65, 2.76, 0.26, 1.28, 4.38, 1.05, 3.4, 1050?

Rather than plotting and measuring, if we only have two observations with two 
features, and we call the the observations $(x_1, y_1)$ and $(x_2,y_2)$ 
we can get the distance using this formula:

$$d = \sqrt{(x_2 - x_1)^2 + (y_2 - y_1)^2}
 $$

For an arbitrary number of dimensions we get this:
$$d = \sqrt{\sum_{i=1}^{n} \left(x_2^{(i)} - x_1^{(i)}\right)^2}$$

Where the weird symbol under the squareroot sign indicates that we add all the 
squared differences between the pairs of observations.

## Is the clustering reproducible?

Not nessecarily. If there _are_ very clear clusters, in general it will be.

But the algorithm is able to find clusters even if there are none:

Here we have 1000 observations of two features:


``` r
test_data <- data.frame(x = rnorm(1000), y = rnorm(1000))
```

They are as random as the random number generator can do it.

Let us make one clustering, and then another:

``` r
set.seed(47)
kmeans_model1 <- kmeans(test_data, 5)
kmeans_model2 <- kmeans(test_data, 5)
```

And then extract the clustering and match them like before:


``` r
tibble(take_one = kmeans_model1$cluster,
        take_two = kmeans_model2$cluster) %>% 
table()
```

``` output
        take_two
take_one   1   2   3   4   5
       1   2 132   0  23   0
       2   2   0 217   0   2
       3   0   0  11   1 124
       4 127  73   9   0   0
       5   0  68   1 121  87
```
Two clusterings on the exact same data. And they are pretty different.

Visualizing it makes it even more apparent:


``` r
tibble(take_one = kmeans_model1$cluster,
        take_two = kmeans_model2$cluster)  %>% 
  cbind(test_data) %>% 
ggplot(aes(x,y,color= factor(take_one))) +
geom_point()
```

<img src="fig/kmeans-rendered-unnamed-chunk-11-1.png" style="display: block; margin: auto;" />



``` r
tibble(take_one = kmeans_model1$cluster,
        take_two = kmeans_model2$cluster)  %>% 
  cbind(test_data) %>% 
ggplot(aes(x,y,color= factor(take_two))) +
geom_point()
```

<img src="fig/kmeans-rendered-unnamed-chunk-12-1.png" style="display: block; margin: auto;" />


## Can it be use on any data?

No. Even though there might actually be clusters in the data, the algorithm
is not nessecarily able to find them. Consider this data:

``` r
test_data <- rbind(
  data.frame(x = rnorm(200, 0,1), y = rnorm(200,0,1), z = rep("A", 200)),
  data.frame(x = runif(1400, -30,30), y = runif(1400,-30,30), z = rep("B", 1400)) %>% 
    filter(abs(x)>10 | abs(y)>10)
  )
test_data %>% 
  ggplot(aes(x,y, color = z)) +
  geom_point()
```

<img src="fig/kmeans-rendered-unnamed-chunk-13-1.png" style="display: block; margin: auto;" />
There is obviously a cluster centered around (0,0). And another cluster more
or lesss evenly spread around it.

The algorithm _will_ find two clusters:


``` r
kmeans_model3 <- kmeans(test_data[,-3], 2)
cluster3 <- augment(kmeans_model3, test_data[,-3])
```

``` error
Error in augment(kmeans_model3, test_data[, -3]): could not find function "augment"
```

``` r
ggplot(cluster3, aes(x,y,color=.cluster)) +
  geom_point()
```

``` error
Error: object 'cluster3' not found
```
But not the ones we want.


::::::::::::::::::::::::::::::::::::: keypoints 

- Use `.md` files for episodes when you want static content

::::::::::::::::::::::::::::::::::::::::::::::::


---
title: 'Elementary Datamanipulation'
teaching: 10
exercises: 2
---

:::::::::::::::::::::::::::::::::::::: questions 

- How do I manipulate the data to have a specific shape?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

- Explain how to use markdown with the new lesson template
- Demonstrate how to include pieces of code, figures, and nested challenge blocks

::::::::::::::::::::::::::::::::::::::::::::::::




## Introduction
Og det er elementær fordi det er de grundlæggende elementer. Så måske et andet
ord? det er elementary som i "periodic table of the elements".

Konceptet er at vi kan manipulere, opsummere og transformere data.
filter(): Filtrerer rækker baseret på betingelser.
select(): Vælger specifikke kolonner.
mutate(): Tilføjer nye kolonner eller ændrer eksisterende.
group_by()
summarise(): Opsummerer data med aggregerede funktioner.
arrange(): Sorterer rækker.
join(): Kombinerer flere datasæt.


## pipen

Vi taler tidy-dialekten her. Så pipen er vigtig. Vi bruger magrittr pipen,
som kan håndtere mere komplekse operationer, og som stadig er meget udbredt når
vi finder hjælp på nettet.

Der findes også den native pipe |> som undertiden vil være hurtigere.


## dplyr

A collection of functions used for changing and filtering rows and columns. 

Det er de basale funktioner, som vi tager i brug når vi har en pæn og tidy
dataframe.

## select

Used for selecting individual variables - otherwise known as columns. Here we
are selecting the columns `species` and `island`:

``` r
penguins %>%
  select(species, island)
```

``` output
# A tibble: 344 × 2
   species island   
   <fct>   <fct>    
 1 Adelie  Torgersen
 2 Adelie  Torgersen
 3 Adelie  Torgersen
 4 Adelie  Torgersen
 5 Adelie  Torgersen
 6 Adelie  Torgersen
 7 Adelie  Torgersen
 8 Adelie  Torgersen
 9 Adelie  Torgersen
10 Adelie  Torgersen
# ℹ 334 more rows
```

For most purposes select is used for paring down the data to something that is
easier to grasp. Having 50 columns can be confusing, especially if we are only 
interested in two. 

Rather than specifying which columns we are interested in, we can specify
which columns we are _not_ interested in:


``` r
penguins %>% 
  select(-c(bill_length_mm, bill_depth_mm, year))
```

``` output
# A tibble: 344 × 5
   species island    flipper_length_mm body_mass_g sex   
   <fct>   <fct>                 <int>       <int> <fct> 
 1 Adelie  Torgersen               181        3750 male  
 2 Adelie  Torgersen               186        3800 female
 3 Adelie  Torgersen               195        3250 female
 4 Adelie  Torgersen                NA          NA <NA>  
 5 Adelie  Torgersen               193        3450 female
 6 Adelie  Torgersen               190        3650 male  
 7 Adelie  Torgersen               181        3625 female
 8 Adelie  Torgersen               195        4675 male  
 9 Adelie  Torgersen               193        3475 <NA>  
10 Adelie  Torgersen               190        4250 <NA>  
# ℹ 334 more rows
```
Collecting the uninteresting columns in a vector, and placing a `-` in front,
we can de-select the specified columns. In this case it is easier to specify
three columns we are not interested in, than specifying the six columns that we
_are_ interested in.

A range of functions for selecting columns exists:

``` r
penguins %>% 
  select(-starts_with("bill"), year)
```

``` output
# A tibble: 344 × 6
   species island    flipper_length_mm body_mass_g sex     year
   <fct>   <fct>                 <int>       <int> <fct>  <int>
 1 Adelie  Torgersen               181        3750 male    2007
 2 Adelie  Torgersen               186        3800 female  2007
 3 Adelie  Torgersen               195        3250 female  2007
 4 Adelie  Torgersen                NA          NA <NA>    2007
 5 Adelie  Torgersen               193        3450 female  2007
 6 Adelie  Torgersen               190        3650 male    2007
 7 Adelie  Torgersen               181        3625 female  2007
 8 Adelie  Torgersen               195        4675 male    2007
 9 Adelie  Torgersen               193        3475 <NA>    2007
10 Adelie  Torgersen               190        4250 <NA>    2007
# ℹ 334 more rows
```
Most can be found in the help for the package `tidyselect`.

A special way of specifying columns is the  `:` notation:


``` r
penguins %>% 
  select(species:bill_depth_mm)
```

``` output
# A tibble: 344 × 4
   species island    bill_length_mm bill_depth_mm
   <fct>   <fct>              <dbl>         <dbl>
 1 Adelie  Torgersen           39.1          18.7
 2 Adelie  Torgersen           39.5          17.4
 3 Adelie  Torgersen           40.3          18  
 4 Adelie  Torgersen           NA            NA  
 5 Adelie  Torgersen           36.7          19.3
 6 Adelie  Torgersen           39.3          20.6
 7 Adelie  Torgersen           38.9          17.8
 8 Adelie  Torgersen           39.2          19.6
 9 Adelie  Torgersen           34.1          18.1
10 Adelie  Torgersen           42            20.2
# ℹ 334 more rows
```

It selects all columns from `species` to `bill_depth_mm` (inclusive).

## filter()

The filter() function filters data, that is returns only the rows that conform
to one or more criteria:


``` r
penguins %>% 
  filter(species == "Adelie")
```

``` output
# A tibble: 152 × 8
   species island    bill_length_mm bill_depth_mm flipper_length_mm body_mass_g
   <fct>   <fct>              <dbl>         <dbl>             <int>       <int>
 1 Adelie  Torgersen           39.1          18.7               181        3750
 2 Adelie  Torgersen           39.5          17.4               186        3800
 3 Adelie  Torgersen           40.3          18                 195        3250
 4 Adelie  Torgersen           NA            NA                  NA          NA
 5 Adelie  Torgersen           36.7          19.3               193        3450
 6 Adelie  Torgersen           39.3          20.6               190        3650
 7 Adelie  Torgersen           38.9          17.8               181        3625
 8 Adelie  Torgersen           39.2          19.6               195        4675
 9 Adelie  Torgersen           34.1          18.1               193        3475
10 Adelie  Torgersen           42            20.2               190        4250
# ℹ 142 more rows
# ℹ 2 more variables: sex <fct>, year <int>
```

The arguments given to filter are predicate functions, functions returning 
either TRUE or FALSE. The expression `species == "Adelie"` compares the values in
the column `species` with the value `"Adelie"`, and returns TRUE for each 
row where the expression is TRUE. Only the rows corresponding to a TRUE value,
are returned.

We are able to do a multitude of logical comparisons. Is bill_lenght_mm larger 
than 40 mm? Simply enter the expression `bill_length > 40`. Does `species` begin
with an "A"? Give filter() the expression str_starts(species, "A")


``` r
penguins %>% 
  filter(str_starts(species, "A"))
```

``` output
# A tibble: 152 × 8
   species island    bill_length_mm bill_depth_mm flipper_length_mm body_mass_g
   <fct>   <fct>              <dbl>         <dbl>             <int>       <int>
 1 Adelie  Torgersen           39.1          18.7               181        3750
 2 Adelie  Torgersen           39.5          17.4               186        3800
 3 Adelie  Torgersen           40.3          18                 195        3250
 4 Adelie  Torgersen           NA            NA                  NA          NA
 5 Adelie  Torgersen           36.7          19.3               193        3450
 6 Adelie  Torgersen           39.3          20.6               190        3650
 7 Adelie  Torgersen           38.9          17.8               181        3625
 8 Adelie  Torgersen           39.2          19.6               195        4675
 9 Adelie  Torgersen           34.1          18.1               193        3475
10 Adelie  Torgersen           42            20.2               190        4250
# ℹ 142 more rows
# ℹ 2 more variables: sex <fct>, year <int>
```

Any expression that can return a TRUE or FALSE value can be used in a filter(), 
and multiple expressions can be added:


``` r
penguins %>% 
  filter(str_starts(species, "A"),
         bill_length_mm > 40 | bill_depth_mm < 20)
```

``` output
# A tibble: 142 × 8
   species island    bill_length_mm bill_depth_mm flipper_length_mm body_mass_g
   <fct>   <fct>              <dbl>         <dbl>             <int>       <int>
 1 Adelie  Torgersen           39.1          18.7               181        3750
 2 Adelie  Torgersen           39.5          17.4               186        3800
 3 Adelie  Torgersen           40.3          18                 195        3250
 4 Adelie  Torgersen           36.7          19.3               193        3450
 5 Adelie  Torgersen           38.9          17.8               181        3625
 6 Adelie  Torgersen           39.2          19.6               195        4675
 7 Adelie  Torgersen           34.1          18.1               193        3475
 8 Adelie  Torgersen           42            20.2               190        4250
 9 Adelie  Torgersen           37.8          17.1               186        3300
10 Adelie  Torgersen           37.8          17.3               180        3700
# ℹ 132 more rows
# ℹ 2 more variables: sex <fct>, year <int>
```
Here we get all the rows, where species starts with an "A", bill_length_mm 
is larger than 40, and bill_depth_mm is smaller than 20. 

## mutate()

The mutate() function allows us to make new columns, based on existing columns:


``` r
penguins %>% 
  select(species, bill_length_mm) %>% 
  mutate(bill_length_inch = bill_length_mm*0.0393701)
```

``` output
# A tibble: 344 × 3
   species bill_length_mm bill_length_inch
   <fct>            <dbl>            <dbl>
 1 Adelie            39.1             1.54
 2 Adelie            39.5             1.56
 3 Adelie            40.3             1.59
 4 Adelie            NA              NA   
 5 Adelie            36.7             1.44
 6 Adelie            39.3             1.55
 7 Adelie            38.9             1.53
 8 Adelie            39.2             1.54
 9 Adelie            34.1             1.34
10 Adelie            42               1.65
# ℹ 334 more rows
```

The syntax specifies the name of the new column, and what it should be equal to.
If we specify an name that already exists in the dataframe, it will be overwritten.
In that way we are able to change an existing column, rather than making a new 
column.

By default mutate adds new columns to the end of the dataframe. A few arguments
`.before` and `.after` allows us to specify a location of the new column.

## summarise() and group_by()

The summarise function allows us to summarise values in columns to a single value:


``` r
penguins %>% 
  summarise(average_weight = mean(body_mass_g, na.rm = TRUE))
```

``` output
# A tibble: 1 × 1
  average_weight
           <dbl>
1          4202.
```
Note that the result is a dataframe. We get the column with the name that we
specified, and a value calculated, here with mean(body_mass_g, na.rm = TRUE).

We are able to calculate more than one summary statistic:


``` r
penguins %>% 
  summarise(average_weight = mean(body_mass_g, na.rm = TRUE),
            std_dev_weight = sd(body_mass_g, na.rm = TRUE))
```

``` output
# A tibble: 1 × 2
  average_weight std_dev_weight
           <dbl>          <dbl>
1          4202.           802.
```
Any function that can take a vector (remember that a column in a dataframe is a 
vector), and return a single value, can be used, including string-functions:


``` r
penguins %>% 
  summarise(a_whole_lot_of_islands = paste0(island, collapse  = ""))
```

``` output
# A tibble: 1 × 1
  a_whole_lot_of_islands                                                        
  <chr>                                                                         
1 TorgersenTorgersenTorgersenTorgersenTorgersenTorgersenTorgersenTorgersenTorge…
```
But try to use more meaningful functions than this example...

`summarise` combined with `filter` allows us to calculate the mean weight
of Adelie penguins:


``` r
penguins %>% 
  filter(species == "Adelie") %>% 
  summarise(avg_weight = mean(body_mass_g, na.rm = TRUE))
```

``` output
# A tibble: 1 × 1
  avg_weight
       <dbl>
1      3701.
```

If we want to do that calculation for each species of penguin, we can group 
the dataframe:


``` r
penguins %>% 
  group_by(species) %>% 
  summarise(avg_weight = mean(body_mass_g, na.rm = TRUE))
```

``` output
# A tibble: 3 × 2
  species   avg_weight
  <fct>          <dbl>
1 Adelie         3701.
2 Chinstrap      3733.
3 Gentoo         5076.
```

`group_by()` will group the dataframe based on the values specified, in this case
"species", and subsequent operations will then be done on each group. 

group_by allows us to group on more than one variable/column. 


``` r
penguins %>% 
  group_by(species, sex) %>% 
  summarise(avg_weight = mean(body_mass_g, na.rm = TRUE))
```

``` output
`summarise()` has grouped output by 'species'. You can override using the
`.groups` argument.
```

``` output
# A tibble: 8 × 3
# Groups:   species [3]
  species   sex    avg_weight
  <fct>     <fct>       <dbl>
1 Adelie    female      3369.
2 Adelie    male        4043.
3 Adelie    <NA>        3540 
4 Chinstrap female      3527.
5 Chinstrap male        3939.
6 Gentoo    female      4680.
7 Gentoo    male        5485.
8 Gentoo    <NA>        4588.
```

Note that the tibble (synonymous with dataframe) returned by summarise, is a grouped
dataframe, grouped by "species". Summarise always removes the last level of grouping.

If we do further operations on this dataframe, they will be done on a grouped
dataframe. We should therefore _always_ add an "ungroup" function to our pipe:


``` r
penguins %>% 
  group_by(species, sex) %>% 
  summarise(avg_weight = mean(body_mass_g, na.rm = TRUE)) %>% 
  ungroup()
```

``` output
`summarise()` has grouped output by 'species'. You can override using the
`.groups` argument.
```

``` output
# A tibble: 8 × 3
  species   sex    avg_weight
  <fct>     <fct>       <dbl>
1 Adelie    female      3369.
2 Adelie    male        4043.
3 Adelie    <NA>        3540 
4 Chinstrap female      3527.
5 Chinstrap male        3939.
6 Gentoo    female      4680.
7 Gentoo    male        5485.
8 Gentoo    <NA>        4588.
```

Doing that assures that subsequent operations happens on an ungrouped dataframe.

## arrange()

Finally - primarily for presentation purposes, we might want to sort a dataframe.

We do that using the `arrange()` function:

``` r
penguins %>% 
  group_by(species, sex) %>% 
  summarise(avg_weight = mean(body_mass_g, na.rm = TRUE)) %>% 
  ungroup() %>% 
  arrange(avg_weight)
```

``` output
`summarise()` has grouped output by 'species'. You can override using the
`.groups` argument.
```

``` output
# A tibble: 8 × 3
  species   sex    avg_weight
  <fct>     <fct>       <dbl>
1 Adelie    female      3369.
2 Chinstrap female      3527.
3 Adelie    <NA>        3540 
4 Chinstrap male        3939.
5 Adelie    male        4043.
6 Gentoo    <NA>        4588.
7 Gentoo    female      4680.
8 Gentoo    male        5485.
```

We can sort on more than one column/variable, and by default `arrange()` sort
in ascending order. If we want to sort in descending order, we would write:

`arrange(desc(avg_weight))` using the `desc()`-helper function.

::::::::::::::::::::::::::::::::::::: keypoints 

- Use `.md` files for episodes when you want static content
- Use `.Rmd` files for episodes when you need to generate output
- Run `sandpaper::check_lesson()` to identify any issues with your lesson
- Run `sandpaper::build_lesson()` to preview your lesson locally

::::::::::::::::::::::::::::::::::::::::::::::::


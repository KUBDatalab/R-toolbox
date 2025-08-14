---
title: 'Table One'
teaching: 10
exercises: 2
---

:::::::::::::::::::::::::::::::::::::: questions 

- How do you make a Table One?
- How do you make a Table One that is easy to configure?

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


``` r
1+1
```

``` output
[1] 2
```


``` error
Error: 'episodes/data/BLOOD.csv' does not exist in current working directory ('/home/runner/work/R-toolbox/R-toolbox/site/built').
```

``` error
Error: object 'blood' not found
```

``` error
Error: object 'blood' not found
```

``` error
Error: object 'blood' not found
```

``` error
Error: object 'blood' not found
```

``` error
Error: object 'blood' not found
```

``` error
Error: object 'blood' not found
```

``` error
Error: object 'blood' not found
```

``` error
Error: object 'blood' not found
```

It can look like this:


``` error
Error: object 'blood' not found
```


Og det gør vi så med gt i stedet.

Herunder er vi ikke helt i mål endnu. Men vi er ret tæt.


``` r
library(dplyr)
library(gtsummary)

# grunddata
base <- blood |>
  select(age, grade, stage, trt) |>
  mutate(grade = paste("Grade", grade))
```

``` error
Error: object 'blood' not found
```

``` r
# rækkefølge på paneler: Overall først, derefter Grade I/II/III
lvl <- c("Overall", paste("Grade", levels(trial$grade)))

# lav et samlet datasæt med et ekstra "Overall"-panel
df <- bind_rows(
  base |> mutate(.panel = "Overall"),
  base |> mutate(.panel = grade)
) |>
  mutate(.panel = factor(.panel, levels = lvl))
```

``` error
Error: object 'base' not found
```

``` r
# tabel: tre strata + et overall-stratum
tbl <- df |>
  tbl_strata(
    strata = .panel,
    .tbl_fun = ~ .x |>
      select(-grade) |> 
      tbl_summary(by = trt, missing = "no") |>
      add_n(),
    .header = "**{strata}**, N = {n}"
  )
```

``` error
Error in `tbl_strata()`:
! The `data` argument must be class <data.frame/survey.design>, not a
  function.
```

``` r
tbl
```

``` output
function(src, ...) {
  UseMethod("tbl")
}
<bytecode: 0x56312b0f2ff8>
<environment: namespace:dplyr>
```

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: instructor

Primarily of use if there are medical students on the course

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::


::::::::::::::::::::::::::::::::::::: keypoints 

- A Table One provides a compact describtion of the data we are working with
- With a little bit of work we can control the content of the table.

::::::::::::::::::::::::::::::::::::::::::::::::


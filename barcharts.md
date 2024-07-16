---
title: 'barcharts'
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

En huskeliste.

De er lette at lave - sværere at få til at se pæne ud.


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
library(palmerpenguins)
```



``` r
penguin_example <- penguins %>% 
  select(species)
```


``` r
penguin_example %>% 
ggplot(aes(x = species)) + 
geom_bar(fill = "blue") +
labs(
  x = element_blank(),
  y = "Number of penguins",
  title = "Number of penguins"
)
```

<img src="fig/barcharts-rendered-unnamed-chunk-3-1.png" style="display: block; margin: auto;" />
Meningsfuld rækkefølge?

De er pt sorteret alfabetisk. Normalt giver det mest mening at sortere dem
efter antal.


``` r
penguin_example %>% 
  mutate(species = fct_infreq(species)) %>% 
ggplot(aes(x = species)) + 
geom_bar(fill = "blue") +
labs(
  x = element_blank(),
  y = "Number of penguins",
  title = "Number of penguins"
)
```

<img src="fig/barcharts-rendered-unnamed-chunk-4-1.png" style="display: block; margin: auto;" />
Det gør det lettere at sammeligne.

Vi har ikke så mange forskellige kategorier - men det er ofte en god ide at
have dem på y-aksen når der er mange.


``` r
penguin_example %>% 
  mutate(species = fct_infreq(species)) %>% 
ggplot(aes(y = species)) + 
geom_bar(fill = "blue") +
labs(
  y = element_blank(),
  x = "Number of penguins",
  title = "Number of penguins"
)
```

<img src="fig/barcharts-rendered-unnamed-chunk-5-1.png" style="display: block; margin: auto;" />
Er teksten stor nok?



``` r
penguin_example %>% 
  mutate(species = fct_infreq(species)) %>% 
ggplot(aes(y = species)) + 
geom_bar(fill = "blue") +
labs(
  y = element_blank(),
  x = "Number of penguins",
  title = "Number of penguins"
) +
  theme_grey(base_size = 14) +
  theme(plot.title = element_text(size = rel(1.1)))
```

<img src="fig/barcharts-rendered-unnamed-chunk-6-1.png" style="display: block; margin: auto;" />
Der er en del tom plads mellem labels og "søjlern" der jo ikke er søjler længere.

``` r
penguin_example %>% 
  mutate(species = fct_infreq(species)) %>% 
ggplot(aes(y = species)) + 
geom_bar(fill = "blue") +
labs(
  y = element_blank(),
  x = "Number of penguins",
  title = "Number of penguins") +
  theme_grey(base_size = 14) +
  theme(plot.title = element_text(size = rel(1.1))) +
  scale_x_continuous(expand = expansion(mult = c(0, 0.01)))
```

<img src="fig/barcharts-rendered-unnamed-chunk-7-1.png" style="display: block; margin: auto;" />
Fjern clutter - det har tufte holdninger til. Der er i hvert fald horisontale
linier der er unødvendige. Og den grå baggrund? det er ikke kønt.


``` r
penguin_example %>% 
  mutate(species = fct_infreq(species)) %>% 
ggplot(aes(y = species)) + 
geom_bar(fill = "blue") +
labs(
  y = element_blank(),
  x = "Number of penguins",
  title = "Number of penguins") +
  theme_grey(base_size = 14) +
  theme(plot.title = element_text(size = rel(1.1))) +
  scale_x_continuous(expand = expansion(mult = c(0, 0.01))) +
  theme_minimal(base_size = 14) +
  theme(
    panel.grid.major.y = element_blank(),
    panel.grid.minor.y = element_blank(),
  )
```

<img src="fig/barcharts-rendered-unnamed-chunk-8-1.png" style="display: block; margin: auto;" />
Vi behøver ikke. Men - det kan være rart at have de rå tal direkte på 
søjlerne. Det er bibliotekets ledelse særligt glade for...


``` r
penguin_count <- count(penguin_example, species)
penguin_example %>% 
  mutate(species = fct_infreq(species)) %>% 
ggplot(aes(y = species)) + 
geom_bar(fill = "blue") +
labs(
  y = element_blank(),
  x = "Number of penguins",
  title = "Number of penguins") +
  theme_grey(base_size = 14) +
  theme(plot.title = element_text(size = rel(1.1))) +
  scale_x_continuous(expand = expansion(mult = c(0, 0.01))) +
  theme_minimal(base_size = 14) +
  theme(
    panel.grid.major.y = element_blank(),
    panel.grid.minor.y = element_blank(),
  ) +
geom_text(data = penguin_count,
mapping = aes(x = n, y = species, label = n),
hjust = 1,
nudge_x = -0.25,
color = "white"
) +
  geom_vline(xintercept = 0) +
  scale_x_continuous(breaks = NULL, expand = expansion(mult = c(0, 0.01))) +
  labs(x = element_blank()) +
  theme(panel.grid.major.x = element_blank(), panel.grid.minor.x = element_blank())
```

``` output
Scale for x is already present.
Adding another scale for x, which will replace the existing scale.
```

<img src="fig/barcharts-rendered-unnamed-chunk-9-1.png" style="display: block; margin: auto;" />

Vi kan lave ca. samme trick med labels hvis vi har lyst.


``` r
penguin_example %>% 
  mutate(species = fct_infreq(species)) %>% 
ggplot(aes(y = species)) + 
geom_bar(fill = "blue") +
labs(
  y = element_blank(),
  x = "Number of penguins",
  title = "Number of penguins") +
  theme_grey(base_size = 14) +
  theme(plot.title = element_text(size = rel(1.1))) +
  scale_x_continuous(expand = expansion(mult = c(0, 0.01))) +
  theme_minimal(base_size = 14) +
  theme(
    panel.grid.major.y = element_blank(),
    panel.grid.minor.y = element_blank(),
  ) +
geom_text(data = penguin_count,
mapping = aes(x = n, y = species, label = n),
hjust = 1,
nudge_x = -0.25,
color = "white" ) +
geom_text(
  data  = penguin_count,
  mapping = aes(x=0,y=species, label = species),
  hjust = 0, nudge_x = 0.25, color = "white", fontface = "bold", size = 4.5) +
geom_vline(xintercept = 0) +
scale_x_continuous(breaks = NULL, expand = expansion(mult = c(0, 0.01))) +
scale_y_discrete(breaks = NULL) +
  labs(x = element_blank()) +
  theme(panel.grid.major.x = element_blank(), panel.grid.minor.x = element_blank())
```

``` output
Scale for x is already present.
Adding another scale for x, which will replace the existing scale.
```

<img src="fig/barcharts-rendered-unnamed-chunk-10-1.png" style="display: block; margin: auto;" />

Det er en smagssag - men det kan se pænere ud med smallere bars.


``` r
penguin_example %>% 
  mutate(species = fct_infreq(species)) %>% 
ggplot(aes(y = species)) + 
geom_bar(fill = "blue",
just = 1, 
width = 0.4) +
labs(
  y = element_blank(),
  x = "Number of penguins",
  title = "Number of penguins") +
  theme_grey(base_size = 14) +
  theme(plot.title = element_text(size = rel(1.1))) +
  scale_x_continuous(expand = expansion(mult = c(0, 0.01))) +
  theme_minimal(base_size = 14) +
  theme(
    panel.grid.major.y = element_blank(),
    panel.grid.minor.y = element_blank(),
  ) +
geom_text(data = penguin_count,
  mapping = aes(x = n, y = species, label = n),
  hjust = 1,
  vjust = 0,
  nudge_y = 0.1,
  color = "black",
  size = 5.5) +
geom_text(
  data  = penguin_count,
  mapping = aes(x=0,y=species, label = species),
  hjust = 0, 
  vjust = 0,
  nudge_x = 0.5, 
  nudge_y = 0.1,
  color = "black", 
  fontface = "bold", 
  size = 4.5) +
geom_vline(xintercept = 0) +
scale_x_continuous(breaks = NULL, expand = expansion(mult = c(0, 0.01))) +
scale_y_discrete(breaks = NULL) +
  labs(x = element_blank()) +
  theme(panel.grid.major.x = element_blank(), panel.grid.minor.x = element_blank())
```

``` output
Scale for x is already present.
Adding another scale for x, which will replace the existing scale.
```

<img src="fig/barcharts-rendered-unnamed-chunk-11-1.png" style="display: block; margin: auto;" />




::::::::::::::::::::::::::::::::::::: keypoints 

- Use `.md` files for episodes when you want static content
- Use `.Rmd` files for episodes when you need to generate output
- Run `sandpaper::check_lesson()` to identify any issues with your lesson
- Run `sandpaper::build_lesson()` to preview your lesson locally

::::::::::::::::::::::::::::::::::::::::::::::::


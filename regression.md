---
title: 'regression'
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

When making a regression, we are attempting to construct a model, explaining a
dependent variable, with one or more independent variables.

We are positing that at variable in our dataset varies, and that that variation
can be explained by variations in other varibles. Ideally variables we can 
control. That allows us to change an independent variable, and confirm that 
the dependent variable actually changes as predicted.

We begin by looking at the simplest case; identifying a linear relationship
between two variables:


Here is some data:


```r
library(tidyverse)
```

```output
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

```r
mtcars %>% 
  ggplot(aes(wt,mpg)) +
  geom_point()
```

<img src="fig/regression-rendered-unnamed-chunk-1-1.png" style="display: block; margin: auto;" />

We are looking at cars, and have their weight (in 1000 pound) on the x-axis, and
their fuel economy (in miles pr gallon) on the y-axis.

It is not much of a surprise, that the heavyer the car, the worse fuel economy.

It also appears that we could draw a straight line through the points, describing
the connection between the two variables.


Let us add a straight line:


```r
mtcars %>% 
  ggplot(aes(wt,mpg)) +
  geom_point() +
  geom_smooth(method = "lm", se = F)
```

```output
`geom_smooth()` using formula = 'y ~ x'
```

<img src="fig/regression-rendered-unnamed-chunk-2-1.png" style="display: block; margin: auto;" />

Not that bad, the connection is not 100%, we are seeing variation.


Lad os prøve at lægge en linie ind:


Ikke helt fjollet. Vi kan tænke på linien som en forudsigelse. Hvis vi ved at 
bilen vejer 4 (og det er i enheden tusinde pund), så finder vi 4 på x-aksen,
går lodret op til linie, vandret til y-aksen, og så kan vi aflæse hvilken
brændstoføkonomi den bil har. 
Vi kan se at der ikke er ret mange af punkterne der ligger præcist på linien.
Det er et udtryk for den fejl der er i vores linie, eller vores model, for biler,
der forudsiger mpg. En given bil med en bestemt vægt har en bestemt brændstoføkonomi.
Og den er normalt forskellig fra hvad vores model forudsiger at den har. Den
forskel kalder vi for et "residual", og den vises i plottet, som afstanden mellem
punktet og linien.
Linien er den vi finder, når vi leder efter en linie, hvor summen af residualerne
er mindst mulig. Der hvor, hvis vi ændrer en lille smule på linien, så bliver den
sum større.
I praksis lægger vi ikke residualerne sammen. Nogen af dem ligger over linien, de
er positive. De der ligger under er negative. Og vi risikerer at deres sum 
bliver 0, at de udligner hinanden, selvom de er meget store.
Derfor kvadrerer vi dem - på den måde er vi sikre på at de alle er positive.

I plottet finder R linien for os. Men hvordan ser den ud?

En ret linie kan beskrives som:


$$ y = ax + b$$

Eller, mere generelt

$$ Y = \beta_0 + \beta_1X$$

$\beta_0$ er skæringen med y-aksen. $\beta_1$ er liniens hælding. Altså hvor
meget værdien af Y på linien ændrer sig når vi ændrer X med 1.

Y omtaler vi ofte som den afhængige variabel, og X som den uafhængige. Det er X
vi i et forsøg kan ændre på. Og så finder vi ud af hvad Y bliver når vi har gjort det.

Øvelsen i en lineær regression er at finde ud af hvilke $\beta$ der giver en
ret linie, der passer bedst med de punkter vi har.

Alle punkterne i vores datasæt har en x-værdi og en y-værdi. Og med vores ligning
kan vi for en given x-værdi beregne hvad y burde være i følge modellen. Den passer
sjælden præcist med de værdier vi har i datasættet.

Derfor beskriver vi ligningen, modellen, lidt mere generelt:

$$ Y = \beta_0 + \beta_1X + \epsilon$$

$\epsilon$ er fejlen. Det er forskellen på hvad modellen siger y er for en 
bestemt værdi af x. Og så det y i virkeligheden er i vores data.

Vi har altså et $\epsilon$ for hver observation i vores datasæt.

Når vi laver sådan en lineær model, finder R de $\beta_0$ og $\beta_1$ der
giver den mindst mulige sum af alle $\epsilon$ for alt vores data. $\epsilon$ kan
være både negativ og positiv. Så i stedet for bare at lægge dem samme, kvadrerer vi dem
først. Det gør at de alle er positive, og derfor ikke kan udligne hinanden. Det 
gør også noget af den bagvedliggende matematik enklere.

Når det er den helt enkle lineære model vi har her, kan vi springe noget matematik over, og 
beregne koefficienterne direkte:

$$\beta_1 = \frac{\sum_{i=1}^{n} (x_i - \overline{x})(y_i - \overline{y})}{\sum_{i=1}^{n} (x_i - \overline{x})^2}$$

$$\beta_0 = \overline{y} - \beta_1\overline{x}$$

Det gider vi ikke selv, det får vi R. Og det gør vi med funktionen lm():



```r
lm(y~x, data = data)
```

y~x er Rs formelnotation, der beskriver at y er en funktion af x.

For vores eksempel ovenfor:


```r
linear_model <- lm(mpg~wt, data = mtcars)
```

Vi gemte resultatet i et objekt, så vi kan arbejde videre med det:


```r
linear_model
```

```output

Call:
lm(formula = mpg ~ wt, data = mtcars)

Coefficients:
(Intercept)           wt  
     37.285       -5.344  
```
Vi får koeeficienterne ud. Skæringen med y-aksen, og hældningen, der fortæller
os at når vi øger bilens vægt med 1 (tusinde pund), så falder mpg, brændstoføkonomien,
med 5.344.

Vi kan få flere detaljer:


```r
summary(linear_model)
```

```output

Call:
lm(formula = mpg ~ wt, data = mtcars)

Residuals:
    Min      1Q  Median      3Q     Max 
-4.5432 -2.3647 -0.1252  1.4096  6.8727 

Coefficients:
            Estimate Std. Error t value Pr(>|t|)    
(Intercept)  37.2851     1.8776  19.858  < 2e-16 ***
wt           -5.3445     0.5591  -9.559 1.29e-10 ***
---
Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

Residual standard error: 3.046 on 30 degrees of freedom
Multiple R-squared:  0.7528,	Adjusted R-squared:  0.7446 
F-statistic: 91.38 on 1 and 30 DF,  p-value: 1.294e-10
```





summary - fortolkning af parametre.

Forudsætning: residualerne skal være normal fordelte. med andre ord, test det!

## Multipel lineær regression

Det er når vi har mere end en uafhængig parameter vi vil bruge til at forklare
den afhængige.

lm(y~x+z, data = data)

Det er årsage tila t vi bruger $\beta$ og nummererer. Det hænger også sammen
med den matematik der foregår bag kulissen, som vi ikke vil bruge tid på.

Teknikken kaldes "ordinary least squares" eller OLS regression. Så det er ikke
så farligt hvis nogen taler om OLS. Det betyder enten at de virkelig ved hvad de
taler om. Eller at de ikke har forstået så meget af det.

yderligere en omgang tolkning af parametre.

lad nu være med at overfitte. Vi kan bygge en model der 100% forklarer data.
Det er ikke det samme som at det er en god model.


## predict!

::::::::::::::::::::::::::::::::::::: keypoints 

- Use `.md` files for episodes when you want static content
- Use `.Rmd` files for episodes when you need to generate output
- Run `sandpaper::check_lesson()` to identify any issues with your lesson
- Run `sandpaper::build_lesson()` to preview your lesson locally

::::::::::::::::::::::::::::::::::::::::::::::::


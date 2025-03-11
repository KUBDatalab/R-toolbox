---
title: 'Choropleths'
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


Hvad er en choropleth.

Et choropleth kort er et tematisk kort, hvor vi farvelægger områder ud fra
data. De viser variationer og mønstre på tværs af regioner, og gør det let at
sammenligne data og finde mønstre mellem forskellige geografiske enheder

Hvad skal man være opmærksom på`

Man skal i hvert fald sikre at data normaliseres korrekt. Hvis vi ønsker at vise
hvor meget vin der fremstilles, skal man være opmærksom på at Frankrigs areal
og befolkningstal 
er en del større end Danmarks. Og at rå tal derfor naturligt vil vise at 
der produceres mere vin i Frankrig end Danmark. Og er det det man ønsker at vise,
eller sammenligne er det fint. Men det kan være man kan lære nyt ved at farvelægge
efter vinproduktionen per kapita. Eller per areal.

Når man klassificerer data skal man også være opmæksom på hvor man lægger breakpoints.
Det er samme koncept som når man laver histogrammer. Små ændringer i breakpoints
kan give store ændringer i kortet. 
Vælg en farvegradient der er intuitiv og let at aflæse. Et godt tip er at 
bruge farver der er venlige overfor farveblinde. De gør det nemlig også lettere at
skelne for folk med normalt farvesyn.
Og så skal man overveje hvordan man trækker de geografiske grænser. Fænomenet kaldes
i GIS-verdenen for MAUP eller "Modifiable Areal Unit Problem". Men er nok i
almindelighed bedre kendt under begrebet gerrymandering. Altså bortset fra at 
MAUP er det man kan komme til hvis man ikke tænker sig om. Mens gerrymandering 
er resulatet når man gør det med vilje.

::::hint
## maup
Skalafølsomhed: Når data aggregeres på forskellige geografiske niveauer (f.eks. postnumre vs. kommuner), kan den observerede statistiske sammenhæng ændre sig.
Zoneringseffekten: Selv inden for samme niveau kan den måde, områderne opdeles på (dvs. hvordan grænserne placeres), påvirke resultaterne.
Fortolkningsusikkerhed: Fordi de aggregerede data er følsomme over for de valgte enheder, skal man være forsigtig med at drage generelle konklusioner ud fra resultaterne.
Analysestrategier: For at minimere MAUP kan man anvende metoder som multiple skalaanalyser eller eksperimentere med alternative zoneringer for at teste robustheden af resultaterne.
Praktisk betydning: MAUP understreger vigtigheden af at have en kritisk tilgang til geografiske data og visualiseringer, da forskellige opdelinger kan lede til forskellige politiske eller økonomiske fortolkninger.


::::



Vi skal bruge to ting for at lave sådan en. Et geospatialt objekt, der har 
data på grænserne på et kort. Det kan være lande grænser, bygrænser, kvarterer,
regioner whatever. Grænser er her skillelinien mellem to geografiske områder,
fordi vi skal farvelægge forskellige områder efter et eller andet.

Vi skal også bruge en variabel, numerisk eller kategorisk. Den skal vi bruge
til at farvelægge områderne i det geografiske objekt. Og vi skal have den
knyttet til en variabel i det geospatiale objekt, der gør at vi kan hægte dem
sammen. Det kan også være de er hægtet sammen allerede.

Find og download en geojson fil. 

Pakken `maps` indeholder en del. Og vi ekstraherer data fra den med ggplot2::map_data(),
der kan trække data ud af maps pakken i et ggplot venligt format.

Vi skal bruge hele verden


``` r
library(tidyverse)
```

``` output
── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
✔ dplyr     1.1.4     ✔ readr     2.1.5
✔ forcats   1.0.0     ✔ stringr   1.5.1
✔ ggplot2   3.5.1     ✔ tibble    3.2.1
✔ lubridate 1.9.4     ✔ tidyr     1.3.1
✔ purrr     1.0.4     
── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
✖ dplyr::filter() masks stats::filter()
✖ dplyr::lag()    masks stats::lag()
ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors
```

``` r
library(maps)
```

``` output

Attaching package: 'maps'

The following object is masked from 'package:purrr':

    map
```

``` r
world <- map_data("world")
```

Hvordan ser det ud?


``` r
world %>% filter(region == "Denmark") %>% head()
```

``` output
      long      lat group order  region subregion
1 11.36143 54.89165   473 35199 Denmark   Lolland
2 11.53838 54.82959   473 35200 Denmark   Lolland
3 11.65810 54.83316   473 35201 Denmark   Lolland
4 11.73955 54.80742   473 35202 Denmark   Lolland
5 11.75898 54.76768   473 35203 Denmark   Lolland
6 11.76592 54.67944   473 35204 Denmark   Lolland
```
Whee! Vi har koordinater på Lolland.
group variable fortæller hvilke koordinater der skal forbindes, og order 
rækkefølgen. Så 11.36143,54.89165 skal forbindes med 11.53838,54.82959.


Det kan vi plotte.


``` r
world %>% 
  ggplot(aes(x = long, y = lat, group = group)) +
  geom_polygon()
```

<img src="fig/choropleths-rendered-unnamed-chunk-3-1.png" style="display: block; margin: auto;" />

lad os generere noget regionsdata.


``` r
data_til_plot <- world %>% 
  select(region) %>% 
  distinct() %>% 
  rowwise() %>% 
  mutate(value = rnorm(1)) %>% 
  ungroup()
```

En række pr region, med navn og en helt tilfældig værdi.

Det kan vi joine:

``` r
world %>% left_join(data_til_plot, by = join_by(region)) %>% 
    ggplot(aes(x = long, y = lat, group = group, fill = value)) +
  geom_polygon() 
```

<img src="fig/choropleths-rendered-unnamed-chunk-5-1.png" style="display: block; margin: auto;" />

Neat. I stedet for helt tilfældige data, ville det være praktisk med "rigtige" 
data.

Vi indlæser noget data for ligestilling mellem kønnene.


``` r
ligestilling <- read_csv("data/BIGI.csv")
```

``` output
Rows: 134 Columns: 9
── Column specification ────────────────────────────────────────────────────────
Delimiter: ","
chr (1): country
dbl (8): rank, overall_rank, BIGI, AADP, basic_education, healthy_life_span,...

ℹ Use `spec()` to retrieve the full column specification for this data.
ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

``` r
head(ligestilling)
```

``` output
# A tibble: 6 × 9
   rank overall_rank country      BIGI    AADP basic_education healthy_life_span
  <dbl>        <dbl> <chr>       <dbl>   <dbl>           <dbl>             <dbl>
1     1           12 Bahrain  -0.00794 0.00794        -0.0126             0     
2     2           24 Great B… -0.0146  0.0157         -0.0121            -0.0332
3     3           27 Netherl… -0.0158  0.0158         -0.0111            -0.0275
4     4           35 Iceland  -0.0177  0.0168         -0.0110            -0.0244
5     5           32 New Zea… -0.0171  0.0171         -0.0103            -0.0272
6     6           30 Serbia   -0.0167  0.0185         -0.00487           -0.0479
# ℹ 2 more variables: life_satisfaction <dbl>, human_development_index <dbl>
```
Det joiner vi på samme måde som før, og farvelægger efter bigi-scoren. Negative værdier
indikerer at drenge og mænd er ringere stillet. Positive at piger og kvinder er 
ringere stillet. Begge relativt til 0-punktet der repræsenterer 
den fuldstændige lige stilling.


``` r
world %>% left_join(ligestilling, by = join_by("region"=="country")) %>% 
    ggplot(aes(x = long, y = lat, group = group, fill = BIGI)) +
  geom_polygon() 
```

<img src="fig/choropleths-rendered-unnamed-chunk-7-1.png" style="display: block; margin: auto;" />
Og her har vi første problem. Det er måske ikke så overraskende at der ikke
er data for antarktis. Men for USA? Og UK?

Det handler om forskellige navneformer.


``` r
world %>% filter(str_detect(region, "US")) %>% 
  distinct(region)
```

``` output
  region
1    USA
```



``` r
ligestilling %>% filter(str_detect(country, "United"))
```

``` output
# A tibble: 3 × 9
   rank overall_rank country       BIGI   AADP basic_education healthy_life_span
  <dbl>        <dbl> <chr>        <dbl>  <dbl>           <dbl>             <dbl>
1    14           45 United Ar… -0.0224 0.0224         -0.0359            0     
2    34           61 United St… -0.0329 0.0329         -0.0201           -0.0476
3    98           92 Tanzania …  0.0520 0.0716          0.155            -0.0294
# ℹ 2 more variables: life_satisfaction <dbl>, human_development_index <dbl>
```

USA hedder USA i kortdata. Og "United States of America" i ligestillingsdata.

Det kan naturligvis ikke hægtes sammen...

Så vi er nødt til at normalisere navneformerne. Det vil vi komme til at støde
på ofte...

Heldigvis er der en pakke der kan hjælpe:

``` r
library(countrycode)
```
contryname() vil tage noget input, og, efter bedste evne, konvertere det til
et fælles format. Der er _mange_ formater. kør ?codelist for at få en ide.

Vi konverterer alle navne til engelsk. Og det skal vi huske at gøre
for begge dataframes.


``` r
ligestilling <- ligestilling %>% 
  mutate(common_name = countryname(country, destination = "cldr.name.en"))

world <- world %>% 
  mutate(common_name = countryname(region, destination = "cldr.name.en"))
```

``` warning
Warning: There was 1 warning in `mutate()`.
ℹ In argument: `common_name = countryname(region, destination =
  "cldr.name.en")`.
Caused by warning:
! Some values were not matched unambiguously: Ascension Island, Azores, Barbuda, Canary Islands, Chagos Archipelago, Grenadines, Heard Island, Madeira Islands, Micronesia, Saba, Saint Martin, Siachen Glacier, Sint Eustatius, Virgin Islands
```

Det er ikke helt perfekt. Regionen "Azores" matcher eksempelvis ikke at navn.
De tilhører Portugal, men det fremgår ikke af vores world datasæt.

Vi er nok tæt nok på til at vi giver det et skud.




``` r
world %>% left_join(ligestilling, by = join_by(common_name==common_name)) %>% 
    ggplot(aes(x = long, y = lat, group = group, fill = BIGI)) +
  geom_polygon() 
```

<img src="fig/choropleths-rendered-unnamed-chunk-12-1.png" style="display: block; margin: auto;" />


der er stadig lande der ikke er med. Men nu skyldes det at der eksempelvis 
ikke er ligestillingsdata for Libyen, og ikke at vi ikke får matchet data.

Mine øjne kan ikke rigtig skelne farverne. Så dem kan vi jo nok med fordel 
gøre noget ved. Her skal vi tage et kig på hvad det faktisk er vi illustrerer. 

Vi har en udfordring i at værdierne af indikatoren kan være positive og 
negative. Men det er en numerisk angivelse, ikke en normativ angivelse.
Så det skal vi have oversat. 

Positive BIGI-scores angiver at piger og kvinder er disadvantaged. Og det er negativt.
Negative BIGI-socres angiver at drenge og mænd er disadvantaged. Og det er positivt.
Og så er der det neutrale nulpunkt. Så vi har brug for en farveskala der har 
tre farver, en normativt positiv, der matcher de negative BIGI-scores, en 
normativt negativ, der matcher de positive BIGI-scores. Og en neutral farve
i midten.

Negative ting får i almindelighed en advarselsfarve - rød,
og ting der er ønskelige en anden, ofte grøn. 

Vi går til colorbrewer, og beder om tre farver der er venlige overfor farveblinde.
For 0 angiver total ligestilling mellem kønnene, og den er nok fin at kunne vise også.

Vi får farverne: #e9a3c9, #f7f7f7 og #a1d76a, der matcher hhv det (normativt) negative,
det neutrale og det positive udfald.

og så beder vi om en farveskala i vores plot der matcher det:

``` r
world %>% left_join(ligestilling, by = join_by(common_name==common_name)) %>% 
    ggplot(aes(x = long, y = lat, group = group, fill = BIGI)) +
  geom_polygon() +
  scale_fill_gradient2(low = "#a1d76a", mid = "#f7f7f7", high = "#e9a3c9", midpoint = 0)
```

<img src="fig/choropleths-rendered-unnamed-chunk-13-1.png" style="display: block; margin: auto;" />

::::challenge
## hvorfor går det så godt i Rusland?

Man kan undre sig over at Rusland er farvet grøn. Ikke ret grøn, men dog grøn.
Hvorfor er kvinder i denne opgørelse stillet bedre end mænd?

Prøv at lave choroplethen for antallet af sunde leveår i stedet. 

::::solution
## Løsningsforslag

``` r
world %>% left_join(ligestilling, by = join_by(common_name==common_name)) %>% 
    ggplot(aes(x = long, y = lat, group = group, fill = healthy_life_span)) +
  geom_polygon() +
  scale_fill_gradient2(low = "#a1d76a", mid = "#f7f7f7", high = "#e9a3c9", midpoint = 0)
```

<img src="fig/choropleths-rendered-unnamed-chunk-14-1.png" style="display: block; margin: auto;" />
Husk, grøn er stadig godt, altså kvinder klarer sig bedre end mænd.
Og på levealder klarer de sig virkelig meget bedre i rusland



::::


::::








::::::::::::::::::::::::::::::::::::: keypoints 

- Use `.md` files for episodes when you want static content
- Use `.Rmd` files for episodes when you need to generate output
- Run `sandpaper::check_lesson()` to identify any issues with your lesson
- Run `sandpaper::build_lesson()` to preview your lesson locally

::::::::::::::::::::::::::::::::::::::::::::::::


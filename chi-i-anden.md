---
title: 'chi-i-anden'
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

# TL;DR

Formatter data på denne måde:

``` r
data
```

``` output
function (..., list = character(), package = NULL, lib.loc = NULL, 
    verbose = getOption("verbose"), envir = .GlobalEnv, overwrite = TRUE) 
{
    fileExt <- function(x) {
        db <- grepl("\\.[^.]+\\.(gz|bz2|xz)$", x)
        ans <- sub(".*\\.", "", x)
        ans[db] <- sub(".*\\.([^.]+\\.)(gz|bz2|xz)$", "\\1\\2", 
            x[db])
        ans
    }
    my_read_table <- function(...) {
        lcc <- Sys.getlocale("LC_COLLATE")
        on.exit(Sys.setlocale("LC_COLLATE", lcc))
        Sys.setlocale("LC_COLLATE", "C")
        read.table(...)
    }
    stopifnot(is.character(list))
    names <- c(as.character(substitute(list(...))[-1L]), list)
    if (!is.null(package)) {
        if (!is.character(package)) 
            stop("'package' must be a character vector or NULL")
    }
    paths <- find.package(package, lib.loc, verbose = verbose)
    if (is.null(lib.loc)) 
        paths <- c(path.package(package, TRUE), if (!length(package)) getwd(), 
            paths)
    paths <- unique(normalizePath(paths[file.exists(paths)]))
    paths <- paths[dir.exists(file.path(paths, "data"))]
    dataExts <- tools:::.make_file_exts("data")
    if (length(names) == 0L) {
        db <- matrix(character(), nrow = 0L, ncol = 4L)
        for (path in paths) {
            entries <- NULL
            packageName <- if (file_test("-f", file.path(path, 
                "DESCRIPTION"))) 
                basename(path)
            else "."
            if (file_test("-f", INDEX <- file.path(path, "Meta", 
                "data.rds"))) {
                entries <- readRDS(INDEX)
            }
            else {
                dataDir <- file.path(path, "data")
                entries <- tools::list_files_with_type(dataDir, 
                  "data")
                if (length(entries)) {
                  entries <- unique(tools::file_path_sans_ext(basename(entries)))
                  entries <- cbind(entries, "")
                }
            }
            if (NROW(entries)) {
                if (is.matrix(entries) && ncol(entries) == 2L) 
                  db <- rbind(db, cbind(packageName, dirname(path), 
                    entries))
                else warning(gettextf("data index for package %s is invalid and will be ignored", 
                  sQuote(packageName)), domain = NA, call. = FALSE)
            }
        }
        colnames(db) <- c("Package", "LibPath", "Item", "Title")
        footer <- if (missing(package)) 
            paste0("Use ", sQuote(paste("data(package =", ".packages(all.available = TRUE))")), 
                "\n", "to list the data sets in all *available* packages.")
        else NULL
        y <- list(title = "Data sets", header = NULL, results = db, 
            footer = footer)
        class(y) <- "packageIQR"
        return(y)
    }
    paths <- file.path(paths, "data")
    for (name in names) {
        found <- FALSE
        for (p in paths) {
            tmp_env <- if (overwrite) 
                envir
            else new.env()
            if (file_test("-f", file.path(p, "Rdata.rds"))) {
                rds <- readRDS(file.path(p, "Rdata.rds"))
                if (name %in% names(rds)) {
                  found <- TRUE
                  if (verbose) 
                    message(sprintf("name=%s:\t found in Rdata.rds", 
                      name), domain = NA)
                  thispkg <- sub(".*/([^/]*)/data$", "\\1", p)
                  thispkg <- sub("_.*$", "", thispkg)
                  thispkg <- paste0("package:", thispkg)
                  objs <- rds[[name]]
                  lazyLoad(file.path(p, "Rdata"), envir = tmp_env, 
                    filter = function(x) x %in% objs)
                  break
                }
                else if (verbose) 
                  message(sprintf("name=%s:\t NOT found in names() of Rdata.rds, i.e.,\n\t%s\n", 
                    name, paste(names(rds), collapse = ",")), 
                    domain = NA)
            }
            files <- list.files(p, full.names = TRUE)
            files <- files[grep(name, files, fixed = TRUE)]
            if (length(files) > 1L) {
                o <- match(fileExt(files), dataExts, nomatch = 100L)
                paths0 <- dirname(files)
                paths0 <- factor(paths0, levels = unique(paths0))
                files <- files[order(paths0, o)]
            }
            if (length(files)) {
                for (file in files) {
                  if (verbose) 
                    message("name=", name, ":\t file= ...", .Platform$file.sep, 
                      basename(file), "::\t", appendLF = FALSE, 
                      domain = NA)
                  ext <- fileExt(file)
                  if (basename(file) != paste0(name, ".", ext)) 
                    found <- FALSE
                  else {
                    found <- TRUE
                    switch(ext, R = , r = {
                      library("utils")
                      sys.source(file, chdir = TRUE, envir = tmp_env)
                    }, RData = , rdata = , rda = load(file, envir = tmp_env), 
                      TXT = , txt = , tab = , tab.gz = , tab.bz2 = , 
                      tab.xz = , txt.gz = , txt.bz2 = , txt.xz = assign(name, 
                        my_read_table(file, header = TRUE, as.is = FALSE), 
                        envir = tmp_env), CSV = , csv = , csv.gz = , 
                      csv.bz2 = , csv.xz = assign(name, my_read_table(file, 
                        header = TRUE, sep = ";", as.is = FALSE), 
                        envir = tmp_env), found <- FALSE)
                  }
                  if (found) 
                    break
                }
                if (verbose) 
                  message(if (!found) 
                    "*NOT* ", "found", domain = NA)
            }
            if (found) 
                break
        }
        if (!found) {
            warning(gettextf("data set %s not found", sQuote(name)), 
                domain = NA)
        }
        else if (!overwrite) {
            for (o in ls(envir = tmp_env, all.names = TRUE)) {
                if (exists(o, envir = envir, inherits = FALSE)) 
                  warning(gettextf("an object named %s already exists and will not be overwritten", 
                    sQuote(o)))
                else assign(o, get(o, envir = tmp_env, inherits = FALSE), 
                  envir = envir)
            }
            rm(tmp_env)
        }
    }
    invisible(names)
}
<bytecode: 0x564287f53560>
<environment: namespace:utils>
```

Testen kan/skal kun bruge tal, så vi skiller os af med den første kolonne. Dernæst:

``` r
chisq.test(data_1)
```

``` error
Error in eval(expr, envir, enclos): object 'data_1' not found
```


# Hvornår bruger vi den?

Når vi sammenligner fordelinger af kategoriske variable. Kategoriske variable er 
dem vi kan tælle. Enten er en observation i den ene kategori. Eller også er den
i en anden kategori. Altså ting der er enten eller - Ja/Nej spørgsmål er et 
eksempel. Et andet kunne være køn, så læge vi kun taler om ikke-menneskelige dyr.

Det behøver ikke være ja/nej spørgsmål, det kan også være antallet af frakturer
på bagbenene af heste. Der kan være en, to eller tre, men ikke 2½.

For at testen er valid skal vi opfylde disse kriterier (https://www.jmp.com/en_au/statistics-knowledge-portal/chi-square-test/chi-square-test-of-independence.html):

* Dataværdierne skal være en simpel tilfældig stikprøve af den population vi er 
interesserede i. 
* To kategoriske (eller nominelle) variable. Vi kani kke bruge uafhængighedstesten 
på kontinuerte variable der definerer kombinationerne af kategorierne. 
Men tælletallene for kombinationerne af de to kategoriske variable vil være
kontinuerte (i det omfang man kan hævde at tælletal som integers er kontinuerte)
* Hvor hver kombination af to værdier/levels af de to variable, skal vi have mindst
fem forventede værdier. Er der færre end fem hvor hver kombination, er resultaterne
ikke pålidelige.


Det gennemgående eksempel her er kønsfordelingen i de fire huse i Hogwarts fra 
Harry Potter universet. Det er drenge eller piger - en kategorisk variabel der
kan tælles. Eller en af Gryffindor, Hufflepuff, Ravenclaw eller Slythering. Også
en kategorisk variabel der kan tælles. Man kan kun være i ét hus, ikke i to.


$\chi^2$ omfatter flere forskellige tests:

* Fordeling - er data fordelt på den forventede måde (hvor man typisk skal 
starte med at tage stilling til hvad man forventer)

* Uafhængighedstest - er to fordelinger uafhængige af hinanden

* Homogenitetstest - kommer to (eller flere) stikprøver fra samme fordeling.

# Test for Uafhængighed

Vi har to variable. Vi vil godt vide om de er relaterede. 

Lad os tage eksemplet.

Vi tæller hvor mange han- og hunkønsvæsener der beskrives i Harry Potter bøgerne,
fordelt på de fire huse. 

Er der forskel på kønsfordelingen i de fire huse?

I stedet for at tælle, hiver vi data ud af KUBDatalabs egen R-pakke. De data
er igen baseret på data trukket fra wikidata.

Først installerer vi den fra Github:

``` r
devtools::install_github("KUBDatalab/KUBDatalab")

library(KUBDatalab)
```

Og så har vi data:


``` r
data <- KUBDatalab::hp_characters
```



Det giver os disse tælletal:


``` r
data %>% kable()
```
Det er de faktuelle optællinger. Vi vil nu gerne vide om der er forskel på 
kønsfordelingen i de fire huse. Ikke om der er forskel på hvor mange hunner 
og hanner der er i Gryffindor. Men om den forskel er forskellig fra forskellen 
i Ravenclaw.

For at finde ud af det, må vi starte med at finde ud af hvad vi ville forvente,
hvis der ikke er forskel.


Når vi skal finde de forventede tælletal, starter vi med række og kolonne totaler:


``` r
data %>% 
  mutate(række_total = female + male) %>% 
  add_row(hus = "kolonne_total", female = 14+5+6+11, male = 20+11+9+14, række_total= 34+16+15+25)
```

Den forventede observation for hvert køn i hvert hus, er baseret på række og kolonne totalerne.

Vi finder den ved for hver celle at gange række totalen med kolone totalen, og 
dividere med den totale sum, eksempelvis for kvindelige gryffindors:

34*36/90 = 13.6

Det vi gør er at beregne hvor stor en andel af alle de karakterer vi vi har talt 
op ialt, uafhængig af deres hus, der er af hunkøn.
Det er i alt: 36/90 = 0.4, eller 40%.

Der er 34 gryffindor i alt, og hvis fordelingen på køn er den samme for alle husene,
skal 40% af vores 34 gryffindors være af hunkøn:

0.4*34 = 13.6

Den beregning laver vi for alle cellerne i vores datasæt, og får:




Det var de forventede værdier. Hvis kønsfordelingen i Slytherin er den samme
som blandt alle karaktererne, så skal der være 10 hunner og 15 hanner. Det er 
de værdier vi skal se, hvis der ikke er nogen forskel på kønsfordelingen blandt
de fire huse.

Hvad gør testen nu? Den ser på forskellen mellem hvad vi talte op, og det vi 
baseret på de tal, ville forvente hvis der ikke var forskel på kønsfordelingen.

Det gør vi i hver celle. Forskelle kvadreres, og divideres med hvad vi forventede:

Eksempelvis for kvindelige gryffindors. Der er 14. det vi forventer er 13.6:

$$(14-13.6)^2/13.6 = 0.01176471$$


Det gør vi så for alle kombinationerne, og lægger tallene sammen.



Lægger vi alle de tal sammen, får vi:

``` r
sum(chisq.test(data_1)$residuals^2)
```

Det er vores "testværdi". Det er det tal vi putter ind i en passende avanceret formel,
og ser hvordan passer med teorien.

I gamle dage havde man tabeller hvor man slog sådan noget op. Det har vi stadig, vi 
bruger dem bare ikke. 
I stedet beder vi computeren om at give os svaret.

Men først beslutter vi os for hvor sikre vi vil være på svaret - altså risikoen
for at de to variable er uafhængige af hinanden, selvom de faktisk er afhængige af 
hinanden.

Det bør vi faktisk beslutte før vi begynder at indsamle data.

Lad os sige at vi accepterer en 5% risiko for at tage fejl i vores vurdering (ja,
p-værdier er lidt mere komplekse at tolke end bare det, men det er den tolkning
de fleste arbejder med, så den bruger vi også her.)

Så beregner vi vores test-statistik. Det var den vi fandt lige før, 0.6966912

Og så finder vi den "kritiske værdi" fra den teoretiske chi-i-anden fordeling,
baseret på vores signifikansniveau (det var de 5%) og på antallet af frihedsgrader.

Frihedsgraderne afhænger af hvor mange kolonner og rækker vi har.
og vi finder dem ved at trække en fra antallet af kolonner, og gange resultatet
med antallet af rækker - 1.

Det giver i dette tilfælde (4-1) * (2-1) = 3

Hvad siger frihedsgraderne? De fortæller os hvor mange variable vi selv kan 
vælge. Vi ved at der er 25 karakterer i Slytherin. Vælger vi at de 11 er kvinder,
er antallet af mænd i Slytherin automatisk givet. Vi ved også at der er 34 
Gryffindors. Hvis vi vælger at 20 af dem er mænd, så er antallet af kvinder
automatisk givet. Vi ved også at der er 16 karakterer i Hufflepuff. Vælger vi at
der skal være 5 kvinder i det hus, er antallet af mænd i Hufflepuff også givet.
Nu har vi valgt tre værdier selv. Og fordi vi ved at der er 36 kvinder i alt i 
datasættet - så er antallet af kvinder i Ravenclaw også givet, og dermed også
antallet af mænd i samme hus. Med andre ord, givet de totale tal, kan vi kun
vælge tre forskellige værdier selv, resten gives automatisk ud fra valgene. Derfor
tre frihedsgrader.

Chi-i-anden værdien for alpha = 0.05, og tre frihedsgrader (df) er 7.815

Hvordan finder vi den? Det gør vi på denne måde:

``` r
qchisq(p=0.05, df=3, lower.tail = F)
```

lower.tail = FALSE angiver at vi får P[X>x]. Det skal der lige graves i.

Det hele koger ned til, at 0.6966912 er mindre end 7.814. Vi havde en NULL-hypotese 
om at køn og hus var uafhængige
de fire huse.

Den hypotese havde vi kunne afvise hvis testværdien, 0.6966912 havde været større end 7.814.

Det kan vi så IKKE. Vi kan derfor ikke afvise NULL-hypotesen. Og selvom det er 
lidt iffy at konkludere at de to variable er uafhængige, fordi vi ikke kan afvise 
at de er uafhængige, så konkluderer vi i almindelighed, at de er uafhængige.

Alt det her gør vi i praksis ikke. Det vi gør er at kaste vores data ind i 
den rigtige funktion:

``` r
chisq.test(data_1)
```
Det giver os vores test-værdi. Men den kigger vi heller ikke på.
Det vi ser på er p-værdien, der fortæller os, at sandsynligheden for at vi ser
den aktuelle forskel på observerede og forventede værdier, hvis kønsfordelingen er 
uafhængig af husene, er .874. 

Summa-summarum, 0.874 er større end 0.05, der er ikke en forskel.


# Hvad får vi ellers:

Det vi umiddelbart får er

``` r
test <- chisq.test(data_1)
```

``` error
Error in eval(expr, envir, enclos): object 'data_1' not found
```

``` r
test
```

``` error
Error in eval(expr, envir, enclos): object 'test' not found
```

Men resultatet indeholder en del anden interessant:



``` r
test$residuals
```
Hvis vi kvadrerer dem, så er det de værdier vi selv fandt ovenfor.

Test-værdien kan trækkes ud på denne måde:



``` r
test$statistic
```


Hvis vi ønsker at se de forventede værdier:

``` r
test$expected
```

``` error
Error in eval(expr, envir, enclos): object 'test' not found
```

Og skulle vi have glemt hvilke tal vi puttede ind i funktionen:

``` r
test$observed
```

``` error
Error in eval(expr, envir, enclos): object 'test' not found
```
Skal vi bruge den beregnede p-værdi, så er den der også:

``` r
test$p.value
```

``` error
Error in eval(expr, envir, enclos): object 'test' not found
```

Tilsvarende med antallet af frihedsgrader:

``` r
test$parameter
```

``` error
Error in eval(expr, envir, enclos): object 'test' not found
```

Har vi glemt hvad det var for en test vi lavede ligger den her:

``` r
test$method
```

``` error
Error in eval(expr, envir, enclos): object 'test' not found
```

::::::::::::::::::::::::::::::::::::: keypoints 

- Use `.md` files for episodes when you want static content
- Use `.Rmd` files for episodes when you need to generate output
- Run `sandpaper::check_lesson()` to identify any issues with your lesson
- Run `sandpaper::build_lesson()` to preview your lesson locally

::::::::::::::::::::::::::::::::::::::::::::::::


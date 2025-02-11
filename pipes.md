---
title: 'A deeper dive into pipes'
teaching: 10
exercises: 2
---

::: questions
-   What are the differences between the two pipes?
:::

::: objectives
-   Explain some of the differences between the two families of pipes
:::

Almost everywhere in this course material, you will see the pipe.

But the story about the pipe is larger than `%>%`.

The `%>%` version come from the `magrittr` package, and was introduced to the R-world in 2014. It became one of the defining features of the R-dialect "tidyverse". It proved useful enough that a similar pipe `|>` was introduced in "base-R" with version 4.1 in May 2021.

::: spoiler
# R dialects

R-programmers tend to write in one dialect of R, small variations that are understandable - but still different. The three common dialects are "base R", the tidyverse and data.table. One of the defining features of the tidyverse dialect has been the magrittr pipe. This differences is eroding with the introduction of the native pipe in base R.

KUB Datalab speaks the tidyverse dialect, and will continue to use the magrittr pipe until the default keyboard shortcut to the pipe in RStudio changes
:::

The base-R pipe function in a similar way to the magrittr pipe. But there are differences:

+-----------------------------+---------------------------------------------------------------------+--------------------------------------+-------------+
| Topic                       | Magrittr 2.0.3                                                      | Base 4.3.0                           | note        |
+=============================+=====================================================================+======================================+=============+
| Operator                    | `%\>%  %\<\>%  %$%  %!>%  %T>%`                                     | `|>`                                 | 1           |
+-----------------------------+---------------------------------------------------------------------+--------------------------------------+-------------+
| Function calls              | `1:2 %>% sum()`                                                     | `1:2 |> sum()`                       |             |
+-----------------------------+---------------------------------------------------------------------+--------------------------------------+-------------+
|                             | **`1:2 %>% sum`**                                                   | Needs the parentheses                |             |
+-----------------------------+---------------------------------------------------------------------+--------------------------------------+-------------+
|                             | `1:3 %>%`\`+\` `4`                                                  | Not every function is supported      |             |
+-----------------------------+---------------------------------------------------------------------+--------------------------------------+-------------+
| Insert on first empty place | `mtcars %>% lm(formula=mpg ~ disp)`                                 | `mtcars |> lm(formula = mpg ~ disp)` | 2           |
+-----------------------------+---------------------------------------------------------------------+--------------------------------------+-------------+
| placeholder                 | .                                                                   | \_                                   |             |
+-----------------------------+---------------------------------------------------------------------+--------------------------------------+-------------+
|                             | `mtcars %>% lm(mpg~disp, data = .)`                                 | `mtcars |> lm(mpg ~ disp, data = _)` |             |
+-----------------------------+---------------------------------------------------------------------+--------------------------------------+-------------+
|                             | `mtcars %>% lm(mpg~disp, .)`                                        | Needs named argument                 | 3           |
+-----------------------------+---------------------------------------------------------------------+--------------------------------------+-------------+
|                             | `1:3 %>% setNames(., .)`                                            | Can only appear once                 |             |
+-----------------------------+---------------------------------------------------------------------+--------------------------------------+-------------+
|                             | `1:3 %>% {sum(sqrt(.))}`                                            | Nested calls not allowed             | 4           |
+-----------------------------+---------------------------------------------------------------------+--------------------------------------+-------------+
| Extraction call             | `mtcars %>% .$cyl`                                                  | `mtcars |> _$cyl`                    |             |
|                             |                                                                     |                                      |             |
|                             | `mtcars %>% {.$cyl[[3]]}`                                           | `mtcars |> _$cyl[[3]]`               |             |
|                             |                                                                     |                                      |             |
|                             | `mtcars %$% cyl[[3]]`                                               |                                      |             |
+-----------------------------+---------------------------------------------------------------------+--------------------------------------+-------------+
| Environment                 | `%>%` has additional function environment. Use: `"x" %!> assign(1)` | `"x" |> assign(1)`                   | 4           |
+-----------------------------+---------------------------------------------------------------------+--------------------------------------+-------------+
| Create Function             | `top6 <- . %>% sort() %>% tail()`                                   | Not possible                         |             |
+-----------------------------+---------------------------------------------------------------------+--------------------------------------+-------------+
| Speed                       | Slower because overhead of function call                            | Faster because syntax transformation | 5           |
+-----------------------------+---------------------------------------------------------------------+--------------------------------------+-------------+

1.  Unless we explicitly import the `magrittr` package, we typically only have access to the simple pipe, `%>%` which is included in many packages. This however is only one of several pipes available from the `magrittr` package. See below.
2.  Note that both pipes insert the input from the left-hand-side at the first *unnamed* argument. Naming other arguments allow us to handle functions that do not take data as the *first* argument.
3.  The placeholder allow us to reference the input in functions. One important difference is that the base-R pipe need to be specified with a named argument. Which is probably best practice anyway.
4.  This is related to the way the pipes actually work, see also 5.
5.  The magrittr pipe works by rewriting the right-hand-side in a function, with its own environment - where the left-hand-side is available with the symbol \`.\`

## The other pipes

As indicated in the comparative table, the `magrittr` pipe have a number of siblings.

### The tee-pipe %T\>%


``` r
DiagrammeR::grViz("digraph {
  graph [layout = dot, rankdir = TB]
  
  node [shape = rectangle]        
  rec1 [label = 'Step 1. Wake up']
  rec2 [label = 'Step 2. Write code']
  rec3 [label =  'Step 3. ???']
  rec4 [label = 'Step 4. PROFIT']
  
  # edge definitions with the node IDs
  rec1 -> rec2 -> rec3 -> rec4
  }",
  height = 500)
```

<div class="figure" style="text-align: center">
<!--html_preserve--><div id="htmlwidget-d4ae20cd2245859497a7" style="width:100%;height:500px;" class="grViz html-widget"></div>
<script type="application/json" data-for="htmlwidget-d4ae20cd2245859497a7">{"x":{"diagram":"digraph {\n  graph [layout = dot, rankdir = TB]\n  \n  node [shape = rectangle]        \n  rec1 [label = \"Step 1. Wake up\"]\n  rec2 [label = \"Step 2. Write code\"]\n  rec3 [label =  \"Step 3. ???\"]\n  rec4 [label = \"Step 4. PROFIT\"]\n  \n  # edge definitions with the node IDs\n  rec1 -> rec2 -> rec3 -> rec4\n  }","config":{"engine":"dot","options":null}},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->
<p class="caption">A diagram showing a programmer's daydream.</p>
</div>



The tee-pipe allow us to inject a function into a longer set of pipes, without affecting the rest of the flow.

<!--html_preserve--><div class="DiagrammeR html-widget html-fill-item" id="htmlwidget-8dbc9e21d805540bd6e6" style="width:504px;height:504px;"></div>
<script type="application/json" data-for="htmlwidget-8dbc9e21d805540bd6e6">{"x":{"diagram":"\n  graph TB\n    A[Input data] --> B[%T>%]\n    B --> C[Run for side-effect <br> e.g. print, plot or log]\n    B --> D[Return original data]\n"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->

%\<\>% %\$% %!\>% %T\>%

The fifth note deserves a bit more explanation.

Den sidste - magrittr pipen er et separat funktions kald hver gang.

Eksempelvis:


``` r
data %>% 
  filter(x > 0) %>% 
  summarise(mean = mean(y))
```

bliver til:


``` r
`%>%`(`%>%`(data, filter(x > 0)), summarize(mean = mean(y)))
```

Mens


``` r
data |>
  filter(x > 0) |>
  summarize(mean = mean(y))
```

bliver til:


``` r
summarize(filter(data, x > 0), mean = mean(y))
```

Og pointen med lm eksemplerne i "insert on first empty place" er at det ikke er på første position der indsættes fra pipen, men på den første position der er ledig. Og når vi specificerer navnet på det første argument, er det ikke ledigt længere.

The other `magrittr` pipes

## What if I want the other pipe?

The keyboard shortcut to get a pipe operator in RStudio is ctrl+shift+m on windows and cmd+shift+m on macos.

This default can be changed:

Tools → Global Options → Code

![](fig/change_pipe.png)

Tools → Keyboard Shortcuts Help. alt-shift-k (Option+Shift+K på mac)

::: keypoints
-   Use `.md` files for episodes when you want static content
-   Use `.Rmd` files for episodes when you need to generate output
-   Run `sandpaper::check_lesson()` to identify any issues with your lesson
-   Run `sandpaper::build_lesson()` to preview your lesson locally
:::

---
title: 'diagramtest'
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
https://github.com/mermaid-js/mermaid#readme
## Introduction

``` r
library(DiagrammeR)
mermaid("
graph LR
    A-->B
")
```

<!--html_preserve--><div class="DiagrammeR html-widget html-fill-item" id="htmlwidget-4d7c2fdab30f40214525" style="width:504px;height:504px;"></div>
<script type="application/json" data-for="htmlwidget-4d7c2fdab30f40214525">{"x":{"diagram":"\ngraph LR\n    A-->B\n"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->

::::::::::::::::::::::::::::::::::::: keypoints 

- Use `.md` files for episodes when you want static content
- Use `.Rmd` files for episodes when you need to generate output
- Run `sandpaper::check_lesson()` to identify any issues with your lesson
- Run `sandpaper::build_lesson()` to preview your lesson locally

::::::::::::::::::::::::::::::::::::::::::::::::


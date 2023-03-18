---
title: 'Top 5 RStudio Addins for productivity and quality of life '
author: Claudiu C. Papasteri
date: '2023-02-23'
slug: top-5-rstudio-addins-for-productivity-and-quality-of-life
categories:
  - R
  - RStudio
tags:
  - R
  - Rstudio
subtitle: ''
summary: ''
authors: []
lastmod: '2023-02-23T21:21:51+02:00'
featured: yes
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
editor_options: 
  markdown: 
    wrap: 140
---

<!--
blogdown::build_site(build_rmd = "E:/Github/cpapasteri.github.io/content/post/2023-02-23-top-5-rstudio-addins-for-productivity-and-quality-of-life/index.en.Rmarkdown")
-->

### Introduction

RStudio Addins let you execute a bit of R code or a Shiny app through the RStudio IDE, either via the Addins drop-down menu or with a
keyboard shortcut.

[Addin list](https://github.com/daattali/addinslist) and corresponding [`addinslist`](https://github.com/daattali/addinslist) package let
you "browse RStudio addins".

This is a list of my top 5 favorite Addins and several very relevant mentions.

 

### Top 5

#### 1. [`GPTStudio`](https://github.com/MichelNivard/gptstudio) for automatic spelling, code commenting and code creation

This package is coupled with [`GPTtools`](https://jameshwade.github.io/gpttools/) and [`OpenAI`](https://irudnyts.github.io/openai/).


```r
# Install
install.packages("openai")
if (!require(remotes))
    install.packages("remotes")
remotes::install_github("MichelNivard/gptstudio")

# For use needs API key
Sys.setenv(OPENAI_API_KEY = "<APIKEY>")    #  OpenAI API key
```

The addin works on text selection:

**"Spelling and Grammar"** 
![](gptstudio_spelling.gif) 
**"Comment your code"** 
![](gptstudio_commentcode.gif) 
**"Write/Code from prompt"**  
![](gptstudio_promptcode.gif)

 

#### 2. Static code analysis (linting) and code styling

-   [`lintr`](https://lintr.r-lib.org/) with `lintr::use_lintr(type = "tidyverse")`

In order to show the "Markers" pane in RStudio: Menu "Tools" -\> "Global Options...", a window with title "Options" will pop up. In that
window: click "Code" on the left; click "Diagnostics" tab; check "Show diagnostics for R".

**"Lint current file"**
![](lintr.gif)

`lintr` is complementary to the `styler` package which automatically restyles code, eliminating some of the problems that `lintr` can
detect.

-   [`styler`](https://styler.r-lib.org/) with `styler::tidyverse_style()`

**"Style section"** / **"Style active file"**

![](styler.gif)

Beyond addin use, `lintr` and `styler` are excellent at R project level, coupled with `precommit` package. Read more about this workflow [on
this r-bloggers post](https://www.r-bloggers.com/2022/09/enforcing-style-in-an-r-project/).

 

#### 3. Search R packages on CRAN

-   [`CRANsearcher`](https://github.com/RhoInc/CRANsearcher) for interactive `CRANsearcher::CRANsearcher()`

-   [`pkgsearch`](https://r-hub.github.io/pkgsearch/reference/pkg_search_addin.html) for `pkgsearch::pkg_search("key words")`


```r
# For addin to work must install:
install.packages("shinyWidgets")
install.packages("whoami")

# pkgsearch::pkg_search_addin(query = "key word", viewer = c("dialog", "browser"))
```

**"CRANsearcher"** / **"CRAN Package Search"**
![](CRANsearcher_pkgsearch.gif)

 

#### 4. Reproducible examples with [`datapasta`](https://milesmcbain.github.io/datapasta/) & [`reprex`](https://reprex.tidyverse.org/)

E.g. use `datapasta::tribble_paste()` from .xlsx to R tibble and then `reprex::reprex({code goes here}, venue = "so")` to make a reproducible
example in a separate environment, copy output to clipboard and then just paste it to StackOverflow. The same steps can be achieved through
addins:

**"Paste as tibble"** + **"Reprex selection"**
![](datapasta_w_reprex_sheet_to_tribble.gif)

 

#### 5. [`caseconverter`](https://github.com/strboul/caseconverter) for name conversions


```r
if (!require(remotes))
  install.packages("remotes")
remotes::install_github("strboul/caseconverter")
```

**"To upper case"** / **"To lower case"** / **"To snake case"**
![](caseconverter.gif)

 

### Important mentions:

#### 6. [`blogdown`](https://pkgs.rstudio.com/blogdown/reference/blogdown.html)

Especially for `blogdown::new_post()` and `blogdown::serve_site()`.

#### 7. [`bookdown`](https://bookdown.org/)

Especially for `bookdown::serve_book()`.

#### 8. Scheduling with [`cronR`](https://github.com/bnosac/cronR) or [`taskscheduleR`](https://github.com/bnosac/taskscheduleR)

`cronR` for Unix/Linux - `cronR::cron_rstudioaddin()` 

`taskscheduleR` for Windows - `taskscheduleR::taskscheduler_create()`


```r
# For taskscheduleR RStudio addin to work, also install:
install.packages("miniUI")
install.packages("shiny")
```

#### 9. [`colourpicker`](https://github.com/daattali/colourpicker)

Especially for `colourpicker::colourWidget()`

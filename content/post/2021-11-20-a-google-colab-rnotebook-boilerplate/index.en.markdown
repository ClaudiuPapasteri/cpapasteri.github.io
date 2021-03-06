---
title: A Google Colab Rnotebook boilerplate
author: Claudiu C. Papasteri
date: '2021-11-20'
slug: a-google-colab-rnotebook-boilerplate
categories:
  - R
tags:
  - Rnotebook
  - google colab
  - googledrive
  - googlesheets4
subtitle: ''
summary: ''
authors: []
lastmod: '2021-11-20T19:28:16+02:00'
featured: yes
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
---

### Motivation

[Google Colab](https://research.google.com/colaboratory/faq.html) is a great free could computing resource. The best part is that it uses Jupyter-like Notebooks that support R runtime out of the box (no need to install kernel or anything), and these notebooks live in your Google Drive <i class="fab fa-google-drive"></i>.

Me and my colleagues use different scripts that needed to be run periodically or on schedule. The problem is that some colleagues do not have R installed and aren't really interested in the code. So for several projects I just bundled up the scripts with an [RPortable](https://sourceforge.net/projects/rportable/) and batch file to run the scrips. Needless to say this was not very efficient.

Now we have a lot of of our data in a shared Google Drive (e.g. some data is coming from SurveyMonkey and is synchronized on Google Drive). This means that my colleagues can run my code in the Google Colab notebook, give their credentials for Google Drive authentication and get their results from the script. It saved a lot of time and it's very intuitive and clean.

### Getting started

#### 1. Create an R Google Colab notebook

-   The easiest way to instantly generate a notebook with R runtime is to click this [link](https://colab.research.google.com/notebook#create=true&language=r).

-   Another way is to create a default one (with Python runtime) and go to "Runtime" → "Change runtime type" and choose R.

-   The third option would be to clone my boilerplate from [GitHub](https://github.com/ClaudiuPapasteri/Google-colab/blob/main/Google_colab_Rnotebook_temp.ipynb). You can run it yourself from [here](https://colab.research.google.com/github/ClaudiuPapasteri/Google-colab/blob/main/Google_colab_Rnotebook_temp.ipynb).

#### 2. Edit the Google Colab notebook

Much like Jupyter or Rnotebooks, it is very easy to get going. Just insert and fill text chunks and code chunks.

#### 3. Link Colab to GitHub and save the notebook in a repo

Follow: "File" → "Save a copy in GitHub". Once that is clicked it will ask for the authorization of your git account. Finally, you will be able to save the collaboratory notebook into the repository of your choice. See more info on the [here](https://cloud.google.com/vertex-ai/docs/workbench/user-managed/save-to-github). 

To open a GitHub file under Colab you can do either of the following:

-   Go to <https://colab.research.google.com> switch to the GitHub tab and enter the URL of the notebook

-   The notebook carries an "Open in Colab" badge, which allows you to open it directly on Google Colab. You can see this badge <img src="https://colab.research.google.com/assets/colab-badge.svg\"> on my [boilerplate](https://github.com/ClaudiuPapasteri/Google-colab/blob/main/Google_colab_Rnotebook_temp.ipynb) and by clicking it, you will be redirected to the Google Colab notebook.

### The [Google Colab Rnotebook boileplate](https://github.com/ClaudiuPapasteri/Google-colab/blob/main/Google_colab_Rnotebook_temp.ipynb)

This boilerplate follows my personal preference for using Google Colab to remotely run non-intensive data analysis scrips remotely and share them with my team.

My workflow works best if:

-   data is located in Google Drive

-   results are tidy and get exported to Google Sheets

#### 1. Global Parameters


```r
options(rlang_interactive = TRUE)  # needed to mount Google Drive

cat("R version: ")
R.Version()$version.string

cat("Number of cores: ")
library(parallel)
parallel::detectCores(all.tests = FALSE, logical = TRUE)
```

The `rlang_interactive` global option is necessary for the Google Drive auth process that is done every session. Here we also check the R version and number of allocated cores as this information is always useful.

#### 2. Install & load packages


```r
load_libs <- c("tidyverse", "googledrive", "googlesheets4")

install_libs <- load_libs[!load_libs %in% installed.packages()]
for(libs in install_libs) install.packages(libs, dependences = TRUE)
sapply(load_libs, require, character = TRUE)
```

Packages are installed and loaded the same way you would do in R.

#### 3. Mount Google Drive


```r
# Mount Google Drive in an R kernel
library("googledrive")
library("googlesheets4")

# Check if is running in Colab and redefine is_interactive()
if (file.exists("/usr/local/lib/python3.7/dist-packages/google/colab/_ipython.py")) {
  suppressMessages({
    install.packages("R.utils")
    library(R.utils)
    library(httr)
    library(rlang)
  })  
  my_check <- function() {return(TRUE)}
  reassignInPackage("is_interactive", pkgName = "httr", my_check) 
  reassignInPackage("is_interactive", pkgName = "rlang", my_check) 
}

# Call Google Drive authentication forcing interactive login and save in cache 
googledrive::drive_auth(use_oob = TRUE, cache = TRUE)
```

This code first loads the Google Drive and Google Sheets interface packages. Then it uses a rather hacky way of mounting Google Drive which gives your Colab notebook access to files on your Drive.

This code may break with changes to Google Colab (e.g. changes to Python version). If it breaks be sure to check this [Gist](Gist:%20https://gist.github.com/jobdiogenes/235620928c84e604c6e56211ccf681f0) for updates.

#### 4. Load data from Google Sheet


```r
# Find Google Sheets 
Sheets <- googledrive::drive_find(type = "spreadsheet")

# Reuse token to Sheet authentification 
googlesheets4::gs4_auth(token = drive_token())

# Read first Google Sheet in Google Drive
sheet_data <- googlesheets4::range_read(Sheets$id[1])
```

Here for demonstration purpose only we read the first Google Sheet we find. Of course we would read a sheet by its URL.

#### 5. Run code from GitHub gist


```r
devtools::source_gist("https://gist.github.com/C-C-P/32869ae032ead6ebb2167554108dad12")    # use also devtools::source_url()
```

#### 6. Run code from GitHub repo


```r
# Define function to source in global env from github url (?raw method) with encoding
source_github_encod <- function(url, encoding = 'UTF-8') {
  raw <- paste(url, "?raw=TRUE", sep = "", collapse = "")
  l <- readLines(raw, encoding = encoding)
  eval(parse(text = l), envir = .GlobalEnv)
}       #devtools::source_url(x, encoding = 'UTF-8')   #  doesn't work every time

source_github_encod("https://github.com/ClaudiuPapasteri/Google-colab/blob/main/PA4/script_surveymonkey-colab-github_code.R")
```

As noted above `devtools::source_url()` should work well for this. But I has some problems with it due to the UTF-8 encoding necessary for special characters. This function opens the raw file from GitHub, reads it using UTF-8 encoding and executes it.

A good idea for working with Google Colab notebooks is to wrap any long or repeated code into functions, commit them to GitHub and source them from there. This will keep your notebook clean and readable.

#### 7. Save data to Google Sheet


```r
# Reuse token to Sheet authentification 
googlesheets4::gs4_auth(token = drive_token())

# Data for export
data_output <- head(iris)

# Name Sheet that will be created
gsheet_name <- paste ("Rnotebook Google colab Sheet", Sys.time(), sep = " ")

# Create Sheet in Google Drive
if(googlesheets4::gs4_has_token()) {
  gsheet_obj <- googlesheets4::gs4_create(
    name = gsheet_name,
    sheets = list(Sheet1 = data_output)    # Data goes in here
  )
}
 
# Check Sheet info
gsheet_obj

# Open a Sheet in default web browser
# Print a Sheet URL
googlesheets4::gs4_get(googlesheets4::gs4_find(gsheet_name))$spreadsheet_url %>%
  httr::BROWSE() 
```

Here we create a new Google Sheet, store our processed data in it (in this demo we just store some rows from the iris dataset). Then we open the Sheet URL in browser window. From here the Sheet may be downloaded as Excel Spreadsheet or later be appended with new data or different sheets.

#### 8. Delete Sheet from Google Drive


```r
# Delete Sheet form Google Drive
googledrive::drive_trash(gsheet_obj)          # good for removing in same session (obj still in memory)
# googlesheets4::gs4_find(gsheet_name) %>%   # good for removing in different session
#   googledrive::drive_trash()
```

Cleaning up after yourself is always recommended [😉](https://emojipedia.org/winking-face/).

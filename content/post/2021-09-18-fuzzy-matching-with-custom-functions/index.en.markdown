---
title: Fuzzy matching with custom functions
author: Claudiu C. Papasteri
date: '2021-09-18'
slug: fuzzy-matching-with-custom-functions
categories:
  - R
tags:
  - stringdist
  - fuzzyjoin
  - fuzzy
  - approximate
  - match
  - join
subtitle: ''
summary: ''
authors: []
lastmod: '2021-09-18T14:00:50+03:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
editor_options: 
  markdown: 
    wrap: 72
---
<script src="{{< blogdown/postref >}}index.en_files/kePrint/kePrint.js"></script>
<link href="{{< blogdown/postref >}}index.en_files/lightable/lightable.css" rel="stylesheet" />

### The problem at hand

Matching multiple datasets based on one or more columns is a very
commonly utilized procedure in data science. Usually, we are lucky
enough to have a common unique identifier between these datasets.

One scenario in which I never come across perfect identifiers is with
faculty work, primarily datasets with student grades. These datasets
have columns like `name`, `email` and `student_id` which may serve as
unique identifiers but sometimes they are not unique (e.g. same name) or
sometimes they fail as identifiers because of human error (e.g. typos).

One of the simple solutions for this problem is to do a
`dplyr::full_join()` on the best unique identifier column, hope for the
best and then sort the unmatched rows by hand. This is cumbersome and
does not allow for smart automation (i.e. every time you have to do this
task it will take you arbitary amounts of time depending on dataset).

Fortunately, we can also use fuzzy matching to address human errors and
match on multiple identifiers in order to make this workflow as robust
as possible by using custom functions tailored to the specifics of each
identifier. Fuzzy matching relies on string distance metrics to arrive
at a decision where to match or not. Using multiple functions to arrive
at a better match was inspired by the [Intermediate Regular Expressions
in R](https://www.datacamp.com/courses/intermediate-regular-expressions-in-r)
course on DataCamp.

#### String distance metrics

There are many flavors of string metrics. We generally distinguish
between edit-based distances, q-gram-based distances, heuristic
distances, kernel-based distances, and distances based on phonetic
algorithms.

##### Phonetic Algorithms

Phonetic algorithms aim to match strings that sound similar when they
are pronounced by transforming a string to some kind of phonetic string
that represents pronunciation features. Most notable distances are
Soundex and the powerful indexing system called Double Metaphone that
replaced it and its successors.

These algorithms are implemented in the package {phonics} and probably
several other. Unfortunately, the original algorithms were developed for
standard English phonetics and are defined only for inputs over the
standard English alphabet, i.e., "A-Z.".

##### q-gram-based distances

Distances based on q-grams are computed by first deriving an integer
vector from strings and defining a distance measure on the space of
vectors. These distances are very useful with long text strings with big
edit distances (e.g. sequence/paragraph exchange) where edit-based
distances may become impractical.

This is not the case with names and other personal identifiers.

##### Notable edit-based distances

The Levenshtein distance is defined to be the minimum number of edit
operations required to transform string a into string b, were edit
operations are:

-   delete

-   insert

-   substitute

-   copy

The optimal string alignment distance, which is also referred to as the
restricted Damerau-Levenshtein distance, allows for all of these edits
and limited transpositions of adjacent characters.

##### Heuristic metrics

The Jaro distance was originally developed at the US bureau of the
Census as part of an application called UNIMATCH. The purpose of this
program was to match records based on inexact text fields, mostly name
and address data.

Jaro-Winkler similarity measure is a variant of the Jaro metric based on
empirical studies that fewer errors typically occur at the beginning of
names.

#### Picking a metric

For our string-matching purposes (matching proper nouns or e-mail
addresses) we will here only consider edit-based distances or heuristic
metrics, although phonetic-based distances and q-gram-based distances
may also be well suited for these data. More specifically, we will end
up using the Jaro-Winkler distance to match names.

### Example data

The hypothetical data was inspired by this python blog
[post](https://towardsdatascience.com/python-tutorial-fuzzy-name-matching-algorithms-7a6f43322cc5).



<table class="table" style="margin-left: auto; margin-right: auto;">
 <thead>
  <tr>
   <th style="text-align:left;"> Challenge </th>
   <th style="text-align:left;"> x </th>
   <th style="text-align:left;"> y </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;background-color: #C0C0C0 !important;"> Phonetic Similarity </td>
   <td style="text-align:left;"> Klaus Werner </td>
   <td style="text-align:left;"> Claus Verner </td>
  </tr>
  <tr>
   <td style="text-align:left;background-color: #C0C0C0 !important;"> Missing Spaces &amp; Hyphens </td>
   <td style="text-align:left;"> Peter Lower </td>
   <td style="text-align:left;"> PeterLower </td>
  </tr>
  <tr>
   <td style="text-align:left;background-color: #C0C0C0 !important;"> Missing Spaces &amp; Hyphens </td>
   <td style="text-align:left;"> Anne Meier-King </td>
   <td style="text-align:left;"> Anne Meier King </td>
  </tr>
  <tr>
   <td style="text-align:left;background-color: #C0C0C0 !important;"> Missing Components </td>
   <td style="text-align:left;"> Peter James Low </td>
   <td style="text-align:left;"> Peter Low </td>
  </tr>
  <tr>
   <td style="text-align:left;background-color: #C0C0C0 !important;"> Abreviaitons </td>
   <td style="text-align:left;"> Jane Mona-King </td>
   <td style="text-align:left;"> Jane M. King </td>
  </tr>
  <tr>
   <td style="text-align:left;background-color: #C0C0C0 !important;"> Out of Order Components </td>
   <td style="text-align:left;"> Joe John Low </td>
   <td style="text-align:left;"> Low John Joe </td>
  </tr>
  <tr>
   <td style="text-align:left;background-color: #C0C0C0 !important;"> Nicknames </td>
   <td style="text-align:left;"> Wiiliam James </td>
   <td style="text-align:left;"> Bill James </td>
  </tr>
  <tr>
   <td style="text-align:left;background-color: #C0C0C0 !important;"> Multiple Languages </td>
   <td style="text-align:left;"> José Müller </td>
   <td style="text-align:left;"> Jose Muller </td>
  </tr>
  <tr>
   <td style="text-align:left;background-color: #C0C0C0 !important;"> Initials </td>
   <td style="text-align:left;"> James Earl Smith </td>
   <td style="text-align:left;"> J.E. Smith </td>
  </tr>
  <tr>
   <td style="text-align:left;background-color: #C0C0C0 !important;"> Typos </td>
   <td style="text-align:left;"> John Bowlby </td>
   <td style="text-align:left;"> Jonh Balby </td>
  </tr>
  <tr>
   <td style="text-align:left;background-color: #C0C0C0 !important;"> Not unique 1 </td>
   <td style="text-align:left;"> John James </td>
   <td style="text-align:left;"> John James </td>
  </tr>
  <tr>
   <td style="text-align:left;background-color: #C0C0C0 !important;"> Not unique 2 &amp; typos </td>
   <td style="text-align:left;"> John James </td>
   <td style="text-align:left;"> John Jams </td>
  </tr>
</tbody>
</table>

The second column to match on that I usually have at my disposal is
either a student ID or an institutional email. Both work virtually the
same way and we can use the same string distance metrics even though
emails contain mostly characters while IDs contain mostly digits. Typos
are the most frequent challenge with these data.

Let's take a look at some sample data. I have kept the name challeges
from above and added an ID column with some errors.


```r
df_x <- 
  tibble::tribble(
              ~name_x,  ~id_x,
       "Klaus Werner", 11991L,
        "Peter Lower", 12621L,
    "Anne Meier-King", 22517L,
    "Peter James Low", 11212L,
     "Jane Mona-King", 21930L,
       "Joe John Low", 22860L,
      "Wiiliam James", 22872L,
        "José Müller", 21259L,
   "James Earl Smith", 21314L,
        "John Bowlby", 12481L,
         "John James", 12298L,
         "John James", 11024L
  )

df_y <- 
  tibble::tribble(
             ~name_y,   ~id_y,
      "Claus Verner",  11199L,
        "PeterLower",  13621L,
   "Anne Meier King",  22517L,
         "Peter Low", 112121L,
      "Jane M. King",  21930L,
      "Low John Joe",   2286L,
        "Bill James",  22872L,
       "Jose Muller",  22259L,
        "J.E. Smith",  22314L,
        "Jonh Balby",  12181L,
        "John James",  12288L,
         "John Jams",  91024L
  )
```

### Fuzzy matching on a single column


```r
# Load packages
library(stringdist)
library(fuzzyjoin)
library(dplyr)
```

Here we will join the data frames on a maximum string distance of 2
using Optimal String Alignment (these are the defaults for {`fuzzyjoin`}
functions like `stringdist_join`).

When working with text data we usually do some preprocessing like
transforming all characters to lower case or excluding special symbols,
stripping white spaces etc.Here we don't need to do preprocessing, we
can just set `ignore_case = TRUE` in the join function of {`fuzzyjoin`}.


```r
## Joining only on names
joined <- fuzzyjoin::stringdist_full_join(
  df_x,
  df_y,
  by = c("name_x" = "name_y"),
  max_dist = 2,                 # default value
  method = "osa",               # default method
  distance_col = "distance",
  ignore_case = TRUE
)

# Print the number of rows of the newly created data frame
print(glue::glue(
  "joining two dataframes with {x} and {y} rows resulted in a dataframe with {z} rows",
  x = nrow(df_x),
  y = nrow(df_y),
  z = nrow(joined)
))
```

```
## joining two dataframes with 12 and 12 rows resulted in a dataframe with 20 rows
```

```r
joined
```

```
## # A tibble: 20 x 5
##    name_x            id_x name_y            id_y distance
##    <chr>            <int> <chr>            <int>    <dbl>
##  1 Klaus Werner     11991 Claus Verner     11199        2
##  2 Peter Lower      12621 PeterLower       13621        1
##  3 Peter Lower      12621 Peter Low       112121        2
##  4 Anne Meier-King  22517 Anne Meier King  22517        1
##  5 José Müller      21259 Jose Muller      22259        2
##  6 John James       12298 John James       12288        0
##  7 John James       12298 John Jams        91024        1
##  8 John James       11024 John James       12288        0
##  9 John James       11024 John Jams        91024        1
## 10 Peter James Low  11212 <NA>                NA       NA
## 11 Jane Mona-King   21930 <NA>                NA       NA
## 12 Joe John Low     22860 <NA>                NA       NA
## 13 Wiiliam James    22872 <NA>                NA       NA
## 14 James Earl Smith 21314 <NA>                NA       NA
## 15 John Bowlby      12481 <NA>                NA       NA
## 16 <NA>                NA Jane M. King     21930       NA
## 17 <NA>                NA Low John Joe      2286       NA
## 18 <NA>                NA Bill James       22872       NA
## 19 <NA>                NA J.E. Smith       22314       NA
## 20 <NA>                NA Jonh Balby       12181       NA
```

We can see that this very permissive procedure made some incorrect
matches and also failed to match some cases.We could change the
`max_dist` to another value to try to optimize the
[sensitivity/specificity
trade-off](https://en.wikipedia.org/wiki/Sensitivity_and_specificity),
but we won't probably go very far with this data.

### Finding matches based on two conditions

We will now try a more sophisticated approach: creating two custom
helper functions that match entries that are similar or equal. We will
use [Jaro--Winkler
distance](https://en.wikipedia.org/wiki/Jaro%E2%80%93Winkler_distance)
to match names and [Damerau-Levenshtein
distance](https://en.wikipedia.org/wiki/Damerau%E2%80%93Levenshtein_distance)
to match IDs.

For more info on distance metrics in the {`fuzzyjoin`} or {`stringdist`}
packages:


```r
# Read more about methods
?stringdist::`stringdist-metrics`
```


```r
# Helper function 1 - Calculate the string distance for names with JW method
is_name_distance_below_threshold <- function(left, right) {
  stringdist::stringdist(left, right, method = "jw") < 1    # set max distance to 1
}

# Helper function 2 - Calculate the string distance for IDs with DL method
is_id_distance_below_threshold <- function(left, right) {
  stringdist::stringdist(left, right, method = "dl") < 2    # set max distance to 2
}

# Join by name and id with our two helper functions
joined <- fuzzyjoin::fuzzy_full_join(
  df_x, df_y,
  by = list(x = c("name_x", "id_x"), y = c("name_y" ,"id_y")),
  match_fun = list(is_name_distance_below_threshold, is_id_distance_below_threshold)
)

# Print the number of rows of the newly created data frame
print(glue::glue(
  "joining two dataframes with {x} and {y} rows resulted in a dataframe with {z} rows",
  x = nrow(df_x),
  y = nrow(df_y),
  z = nrow(joined)
))
```

```
## joining two dataframes with 12 and 12 rows resulted in a dataframe with 13 rows
```

```r
joined
```

```
## # A tibble: 13 x 4
##    name_x            id_x name_y            id_y
##    <chr>            <int> <chr>            <int>
##  1 Peter Lower      12621 PeterLower       13621
##  2 Anne Meier-King  22517 Anne Meier King  22517
##  3 Peter James Low  11212 Peter Low       112121
##  4 Jane Mona-King   21930 Jane M. King     21930
##  5 Joe John Low     22860 Low John Joe      2286
##  6 Wiiliam James    22872 Bill James       22872
##  7 José Müller      21259 Jose Muller      22259
##  8 James Earl Smith 21314 J.E. Smith       22314
##  9 John Bowlby      12481 Jonh Balby       12181
## 10 John James       12298 John James       12288
## 11 John James       11024 John Jams        91024
## 12 Klaus Werner     11991 <NA>                NA
## 13 <NA>                NA Claus Verner     11199
```

Interestingly, only the phonetically similar name with very different ID
remained unmatched.



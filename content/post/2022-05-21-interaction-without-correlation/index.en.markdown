---
title: Interaction / Moderation with or without Correlation / Association
author: Claudiu C. Papasteri
date: '2022-05-21'
slug: interaction-without-correlation
categories:
  - R
tags:
  - stats
  - simulation
  - interaction
  - moderation
  - regression
  - ANOVA
  - ggplot2
subtitle: ''
summary: ''
authors: []
lastmod: '2022-05-21T17:15:40+03:00'
featured: yes
image:
  caption: ''
  focal_point: ''
  preview_only: yes
projects: []
---

### I. The Question

One of the most common stats questions I got this year was **“Can there be interaction/moderation without correlation/association?”**.
Of course, the short answer is **yes**, significant interaction/moderation exist without any correlation/association, and *vice versa*.

The longer answer, I prefer to split into two discussion:
- The first will be a **simulation-based answer** aimed at alleviating doubt about the factual nature of the answer. In this virtual tour we can make uncorrelated variables interact to explain outcomes, or coerce correlated variables not to have interaction effects. We can create at will artificial interactions or obscure real ones in our analyses.  
- The second will be an **empirical and rational answer** that aims to return the reader to the real world where human logic and instrumentation meets the irreducible complexity of our universe. Here we discuss that most outcomes are produced by interactions, but that our universe seems to obey a hierarchical ordering of effects principle, so correlation should be much more common than interaction. Nevertheless, in real world science, our main concern should remain on weed out spurious or artifactual relationships - be it correlations or interactions - and this task cannot be accomplished by statistics alone.

I will try to do most of the work in base R and will use packages only for data simulation, as well as plotting and table rendering.
Used pacakg:
- `MASS` for mvrnorm() to simulate multivariate normally distributed data;
- `bindata` for rmvbin() to simulate multivariate binary data;
- `ggplot2` for plotting;
- `cowplot` for pasting plots together;
- `gt` for pretty html tables; also exports pipe operator %&gt;%;
- `gtsummary` for pretty html tables of lm() or aov() objects.

Also `gt_apa_style()` and `theme_apa()` are declared silently (see this post’s code for references of these functions) and are only useful for APA style table rendering and plotting, respectively.

<style type="text/css">
.gt_table {
  margin-left: auto !important;
  margin-right: auto !important;
  width: 90% !important;
  overflow: hidden !important;
}
</style>

<br>
<br>
<br>

### II. Simulation-based answer

#### II. 1. Interaction of continuous variables

Here we will demonstrate how to simulate data with weakly correlated variables that have interaction effects. We will use the simple OLS Regression formula to do this.

Regression:
$$
y = \beta_0 + \beta_1X_1 + \beta_2X_2 + \beta_3X_1X_2 + e
$$

##### 1) Simulate data

Simulate data for two uncorrelated standard normal variables. Use OLS formula to simulate a third variable that is associated with the product (multiplicative interaction) of the first two.

``` r
## Simple OLS simulation - moderation/interaction without correlation
set.seed(42)

# Construct a correlation matrix for two uncorrelated normal variables
rho <- 0
m <- matrix(c(1, 0, 0, 1), nrow = 2)

# Simulate 200 data points for x1 and x2 that are normally distributed and uncorrelated 
data <-  MASS::mvrnorm(n = 200, mu = c(0, 0), Sigma = m, empirical = TRUE)
data <- as.data.frame(data)
colnames(data) <- c("x1", "x2")
data$x1x2 <- data$x1 * data$x2     # interaction term is simple multiplication of x1 and x2

# Simulate outcome y according with OLS equation
beta1 <- .0001
beta2 <- .0001
beta3 <- .60
error <- rnorm(200, 0, sqrt(1 - (beta1^2 + beta2^2 + beta3^2)))
data$y <- beta1*data$x1 + beta2*data$x2 + beta3*data$x1x2 + error
```

##### 2) Check the correlation table

``` r
# Check correlation
data[, c("x1", "x2", "y")] %>% 
  cor() %>% 
  as.data.frame() %>% 
  round(2) %>% 
  gt::gt(rownames_to_stub = TRUE) %>% 
  gt::tab_header(title = "Correlation Table") %>%
  gt_apa_style(container.width = "50%", container.overflow.x = FALSE)  # hacky way of not showing y scroll bar when rendering
```

<div id="bfbpyieqlt" style="overflow-x:hidden;overflow-y:auto;width:50%;height:auto;">
<style>html {
  font-family: times, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Helvetica Neue', 'Fira Sans', 'Droid Sans', Arial, sans-serif;
}

#bfbpyieqlt .gt_table {
  display: table;
  border-collapse: collapse;
  margin-left: auto;
  margin-right: auto;
  color: #333333;
  font-size: 12px;
  font-weight: normal;
  font-style: none;
  background-color: #FFFFFF;
  width: auto;
  border-top-style: none;
  border-top-width: 2px;
  border-top-color: #FFFFFF;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #A8A8A8;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
}

#bfbpyieqlt .gt_heading {
  background-color: #FFFFFF;
  text-align: left;
  border-bottom-color: #FFFFFF;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}

#bfbpyieqlt .gt_title {
  color: #333333;
  font-size: 12px;
  font-weight: initial;
  padding-top: 4px;
  padding-bottom: 4px;
  border-bottom-color: #FFFFFF;
  border-bottom-width: 0;
}

#bfbpyieqlt .gt_subtitle {
  color: #333333;
  font-size: 12px;
  font-weight: initial;
  padding-top: 0;
  padding-bottom: 6px;
  border-top-color: #FFFFFF;
  border-top-width: 0;
}

#bfbpyieqlt .gt_bottom_border {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #000000;
}

#bfbpyieqlt .gt_col_headings {
  border-top-style: none;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 1px;
  border-bottom-color: #000000;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}

#bfbpyieqlt .gt_col_heading {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 6px;
  padding-left: 5px;
  padding-right: 5px;
  overflow-x: hidden;
}

#bfbpyieqlt .gt_column_spanner_outer {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  padding-top: 0;
  padding-bottom: 0;
  padding-left: 4px;
  padding-right: 4px;
}

#bfbpyieqlt .gt_column_spanner_outer:first-child {
  padding-left: 0;
}

#bfbpyieqlt .gt_column_spanner_outer:last-child {
  padding-right: 0;
}

#bfbpyieqlt .gt_column_spanner {
  border-bottom-style: solid;
  border-bottom-width: 1px;
  border-bottom-color: #000000;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 5px;
  overflow-x: hidden;
  display: inline-block;
  width: 100%;
}

#bfbpyieqlt .gt_group_heading {
  padding: 8px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-top-style: none;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
}

#bfbpyieqlt .gt_empty_group_heading {
  padding: 0.5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  border-top-style: none;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: middle;
}

#bfbpyieqlt .gt_from_md > :first-child {
  margin-top: 0;
}

#bfbpyieqlt .gt_from_md > :last-child {
  margin-bottom: 0;
}

#bfbpyieqlt .gt_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  margin: 10px;
  border-top-style: none;
  border-top-width: 1px;
  border-top-color: #FFFFFF;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
  overflow-x: hidden;
}

#bfbpyieqlt .gt_stub {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 12px;
}

#bfbpyieqlt .gt_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#bfbpyieqlt .gt_first_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-style: none;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
}

#bfbpyieqlt .gt_grand_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#bfbpyieqlt .gt_first_grand_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-style: none;
  border-top-width: 6px;
  border-top-color: #D3D3D3;
}

#bfbpyieqlt .gt_striped {
  background-color: rgba(128, 128, 128, 0.05);
}

#bfbpyieqlt .gt_table_body {
  border-top-style: solid;
  border-top-width: 1px;
  border-top-color: #000000;
  border-bottom-style: solid;
  border-bottom-width: 1px;
  border-bottom-color: #000000;
}

#bfbpyieqlt .gt_footnotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}

#bfbpyieqlt .gt_footnote {
  margin: 0px;
  font-size: 90%;
  padding: 4px;
}

#bfbpyieqlt .gt_sourcenotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}

#bfbpyieqlt .gt_sourcenote {
  font-size: 90%;
  padding: 4px;
}

#bfbpyieqlt .gt_left {
  text-align: left;
}

#bfbpyieqlt .gt_center {
  text-align: center;
}

#bfbpyieqlt .gt_right {
  text-align: right;
  font-variant-numeric: tabular-nums;
}

#bfbpyieqlt .gt_font_normal {
  font-weight: normal;
}

#bfbpyieqlt .gt_font_bold {
  font-weight: bold;
}

#bfbpyieqlt .gt_font_italic {
  font-style: italic;
}

#bfbpyieqlt .gt_super {
  font-size: 65%;
}

#bfbpyieqlt .gt_footnote_marks {
  font-style: italic;
  font-weight: normal;
  font-size: 65%;
}
</style>
<table class="gt_table">
  <thead class="gt_header">
    <tr>
      <th colspan="4" class="gt_heading gt_title gt_font_normal gt_bottom_border" style>Correlation Table</th>
    </tr>
    
  </thead>
  <thead class="gt_col_headings">
    <tr>
      <th class="gt_col_heading gt_columns_bottom_border gt_left" rowspan="1" colspan="1"></th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1">x1</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1">x2</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1">y</th>
    </tr>
  </thead>
  <tbody class="gt_table_body">
    <tr><td class="gt_row gt_left gt_stub">x1</td>
<td class="gt_row gt_right">1.00</td>
<td class="gt_row gt_right">0.00</td>
<td class="gt_row gt_right">0.08</td></tr>
    <tr><td class="gt_row gt_left gt_stub">x2</td>
<td class="gt_row gt_right">0.00</td>
<td class="gt_row gt_right">1.00</td>
<td class="gt_row gt_right">-0.03</td></tr>
    <tr><td class="gt_row gt_left gt_stub">y</td>
<td class="gt_row gt_right">0.08</td>
<td class="gt_row gt_right">-0.03</td>
<td class="gt_row gt_right">1.00</td></tr>
  </tbody>
  
  
</table>
</div>

<br>

##### 3) Check the regression table

``` r
# Check linear regression output
lm_fit <- lm(y ~ x1 + x2, data = data)
lm_fit_int <- lm(y ~ x1 + x2 + x1:x2, data = data) 

gtsummary::tbl_regression(lm_fit_int) %>% 
  gtsummary::bold_p() %>%
  gtsummary::as_gt(include = -tab_footnote) %>%
  gt::tab_header(title = "Regression Table") %>%
  gt_apa_style() %>%
  gt_apa_style(container.width = "65%", container.overflow.x = FALSE)  # hacky way of not showing y scroll bar when rendering
```

<div id="dfbcrqxrkh" style="overflow-x:hidden;overflow-y:auto;width:65%;height:auto;">
<style>html {
  font-family: times, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Helvetica Neue', 'Fira Sans', 'Droid Sans', Arial, sans-serif;
}

#dfbcrqxrkh .gt_table {
  display: table;
  border-collapse: collapse;
  margin-left: auto;
  margin-right: auto;
  color: #333333;
  font-size: 12px;
  font-weight: normal;
  font-style: none;
  background-color: #FFFFFF;
  width: auto;
  border-top-style: none;
  border-top-width: 2px;
  border-top-color: #FFFFFF;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #A8A8A8;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
}

#dfbcrqxrkh .gt_heading {
  background-color: #FFFFFF;
  text-align: left;
  border-bottom-color: #FFFFFF;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}

#dfbcrqxrkh .gt_title {
  color: #333333;
  font-size: 12px;
  font-weight: initial;
  padding-top: 4px;
  padding-bottom: 4px;
  border-bottom-color: #FFFFFF;
  border-bottom-width: 0;
}

#dfbcrqxrkh .gt_subtitle {
  color: #333333;
  font-size: 12px;
  font-weight: initial;
  padding-top: 0;
  padding-bottom: 6px;
  border-top-color: #FFFFFF;
  border-top-width: 0;
}

#dfbcrqxrkh .gt_bottom_border {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #000000;
}

#dfbcrqxrkh .gt_col_headings {
  border-top-style: none;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 1px;
  border-bottom-color: #000000;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}

#dfbcrqxrkh .gt_col_heading {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 6px;
  padding-left: 5px;
  padding-right: 5px;
  overflow-x: hidden;
}

#dfbcrqxrkh .gt_column_spanner_outer {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  padding-top: 0;
  padding-bottom: 0;
  padding-left: 4px;
  padding-right: 4px;
}

#dfbcrqxrkh .gt_column_spanner_outer:first-child {
  padding-left: 0;
}

#dfbcrqxrkh .gt_column_spanner_outer:last-child {
  padding-right: 0;
}

#dfbcrqxrkh .gt_column_spanner {
  border-bottom-style: solid;
  border-bottom-width: 1px;
  border-bottom-color: #000000;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 5px;
  overflow-x: hidden;
  display: inline-block;
  width: 100%;
}

#dfbcrqxrkh .gt_group_heading {
  padding: 8px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-top-style: none;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
}

#dfbcrqxrkh .gt_empty_group_heading {
  padding: 0.5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  border-top-style: none;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: middle;
}

#dfbcrqxrkh .gt_from_md > :first-child {
  margin-top: 0;
}

#dfbcrqxrkh .gt_from_md > :last-child {
  margin-bottom: 0;
}

#dfbcrqxrkh .gt_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  margin: 10px;
  border-top-style: none;
  border-top-width: 1px;
  border-top-color: #FFFFFF;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
  overflow-x: hidden;
}

#dfbcrqxrkh .gt_stub {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 12px;
}

#dfbcrqxrkh .gt_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#dfbcrqxrkh .gt_first_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-style: none;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
}

#dfbcrqxrkh .gt_grand_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#dfbcrqxrkh .gt_first_grand_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-style: none;
  border-top-width: 6px;
  border-top-color: #D3D3D3;
}

#dfbcrqxrkh .gt_striped {
  background-color: rgba(128, 128, 128, 0.05);
}

#dfbcrqxrkh .gt_table_body {
  border-top-style: solid;
  border-top-width: 1px;
  border-top-color: #000000;
  border-bottom-style: solid;
  border-bottom-width: 1px;
  border-bottom-color: #000000;
}

#dfbcrqxrkh .gt_footnotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}

#dfbcrqxrkh .gt_footnote {
  margin: 0px;
  font-size: 90%;
  padding: 4px;
}

#dfbcrqxrkh .gt_sourcenotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}

#dfbcrqxrkh .gt_sourcenote {
  font-size: 90%;
  padding: 4px;
}

#dfbcrqxrkh .gt_left {
  text-align: left;
}

#dfbcrqxrkh .gt_center {
  text-align: center;
}

#dfbcrqxrkh .gt_right {
  text-align: right;
  font-variant-numeric: tabular-nums;
}

#dfbcrqxrkh .gt_font_normal {
  font-weight: normal;
}

#dfbcrqxrkh .gt_font_bold {
  font-weight: bold;
}

#dfbcrqxrkh .gt_font_italic {
  font-style: italic;
}

#dfbcrqxrkh .gt_super {
  font-size: 65%;
}

#dfbcrqxrkh .gt_footnote_marks {
  font-style: italic;
  font-weight: normal;
  font-size: 65%;
}
</style>
<table class="gt_table">
  <thead class="gt_header">
    <tr>
      <th colspan="4" class="gt_heading gt_title gt_font_normal gt_bottom_border" style>Regression Table</th>
    </tr>
    
  </thead>
  <thead class="gt_col_headings">
    <tr>
      <th class="gt_col_heading gt_columns_bottom_border gt_left" rowspan="1" colspan="1"><strong>Characteristic</strong></th>
      <th class="gt_col_heading gt_columns_bottom_border gt_center" rowspan="1" colspan="1"><strong>Beta</strong></th>
      <th class="gt_col_heading gt_columns_bottom_border gt_center" rowspan="1" colspan="1"><strong>95% CI</strong></th>
      <th class="gt_col_heading gt_columns_bottom_border gt_center" rowspan="1" colspan="1"><strong>p-value</strong></th>
    </tr>
  </thead>
  <tbody class="gt_table_body">
    <tr><td class="gt_row gt_left">x1</td>
<td class="gt_row gt_center">0.03</td>
<td class="gt_row gt_center">-0.09, 0.14</td>
<td class="gt_row gt_center">0.6</td></tr>
    <tr><td class="gt_row gt_left">x2</td>
<td class="gt_row gt_center">0.05</td>
<td class="gt_row gt_center">-0.07, 0.17</td>
<td class="gt_row gt_center">0.4</td></tr>
    <tr><td class="gt_row gt_left">x1 * x2</td>
<td class="gt_row gt_center">0.51</td>
<td class="gt_row gt_center">0.39, 0.62</td>
<td class="gt_row gt_center" style="font-weight: bold;"><0.001</td></tr>
  </tbody>
  
  
</table>
</div>

<br>

##### 4) Fitted vs Actual Plot

We will fit two regression models. The first comprises only the additive effects (i.e. only the predictors, no interaction term), while the second is the full model comprising both predictors and their interaction. From the two plots it is clear that the interaction term brings most of the explanatory power.

``` r
# Fitted vs Actual Plot
data_lm_fit <- data.frame(Predicted = predict(lm_fit), Observed = data$y)
data_lm_fit_int <- data.frame(Predicted = predict(lm_fit_int), Observed = data$y)

obspred_plot1 <- 
  ggplot(data_lm_fit, aes(x = Predicted, y = Observed)) +
  geom_point() +
  geom_abline(intercept = 0, slope = 1, color = "red", size = 2) +
  ggtitle("Predicted vs. Actual Plot", subtitle = "Regression model without interaction term") +
  theme_apa()

obspred_plot2 <- 
  ggplot(data_lm_fit_int, aes(x = Predicted, y = Observed)) +
  geom_point() +
  geom_abline(intercept = 0, slope = 1, color = "red", size = 2) +
  ggtitle("Predicted vs. Actual Plot", subtitle = "Regression model with interaction term") +
  theme_apa()

cowplot::plot_grid(obspred_plot1, obspred_plot2)
```

<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-6-1.png" width="672" />

##### 4) Simple Slopes Plot

For us humans, understanding interactions between continuous variables is hard. This is why a standard visualization named “Simple Slopes” tries to convey the interactions meaning by categorizing one of the predictors. This of course, is an oversimplification of the relationship, but it is one that we can readily understand. Now, the effect of the interaction is clear as day.

``` r
# Simple slopes plot
modxnames <- c("Mean - 1SD",
               "Mean",
               "Mean + 1SD")
modxvals <- c(mean(data$x2) - sd(data$x2),  
               mean(data$x2),
               mean(data$x2) + sd(data$x2))
mod_val_qs <- ecdf(data$x2)(sort(modxvals))  # Use ecdf function to get quantile of the modxvals
cut_points <- c() 
for (i in 1:(length(modxvals) - 1)) {                             # split the data in a way that roughly puts each modxvals in the middle of each group
  cut_points <- c(cut_points, mean(mod_val_qs[i:(i + 1)]))        # see https://github.com/jacob-long/interactions/blob/master/R/int_utils.R
}
cut_points <- c(-Inf, quantile(data$x2, cut_points, na.rm = TRUE), Inf)
data$x2_groups <- cut(data$x2, cut_points, labels = modxnames)

sslope_plot <- 
  ggplot(data, aes(x = x1, y = y, group = x2_groups, lty = x2_groups, color = x2_groups)) +
    geom_smooth(method = "lm", formula = y ~ x) +
    geom_point(alpha = 0.5) +
    ggtitle("Simple Slopes Plot") +
    theme_apa()

sslope_plot
```

<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-7-1.png" width="672" />

<br>
<br>
<br>

#### II. 2. Interaction with categorical variables

As we have seen with the Simple Slopes procedure, interaction/moderation is easiest to understand when it between fewer, rather multiple, and dichotomous, rather than polytomous or continuous.

Here we will use the example of 2x2 Between Subjects Factorial ANOVA, where A and B are *independent* variables.

The data we are about to simulate mimics two types of very common data sources:
- a cross-sectional observational study (with independent groups): A and B may be both changing or unchanging subject characteristics (e.g. A = male/female, B = urban/rural)
- a 2x2 factorial experimental design (with independent groups): A and B must be experimental manipulations, and may also comprise changing or unchanging subject characteristics (e.g. A = treatment/placebo, B = male/female)

2x2 ANOVA:
$$
y = \beta_0 + \beta_1A + \beta_2B + \beta_3AB + e
$$

##### 1) Simulate data

Simulate data for two uncorrelated binary variables. Use OLS formula to simulate a third variable that is associated with the product (multiplicative interaction) of the first two.

``` r
## 2x2 ANOVA model simulation
set.seed(42)

# Construct a correlation matrix for two uncorrelated binary variables
rho <- 0
m <- matrix(c(1, rho, rho, 1), nrow = 2) 

# Simulate 200 data points for factors A and B that are binary and uncorrelated 
data <-  bindata::rmvbin(n = 200, margprob = c(0.25, 0.25), bincorr = m)
```

    ## Warning in regularize.values(x, y, ties, missing(ties), na.rm = na.rm):
    ## collapsing to unique 'x' values

``` r
data <- as.data.frame(data)
colnames(data) <- c("A", "B")
data$AB <- data$A * data$B     # interaction term is simple multiplication of x1 and x2

# Simulate outcome y according with OLS equation
beta1 <- .0001
beta2 <- .0001
beta3 <- .82
error <- rnorm(200, 0, sqrt(1 - (beta1^2 + beta2^2 + beta3^2)))
data$y <- beta1*data$A + beta2*data$B + beta3*data$AB + error
```

##### 2) Check the correlation table

``` r
# Check correlation
data[, c("A", "B", "y")] %>% 
  cor() %>% 
  as.data.frame() %>% 
  round(2) %>% 
  gt::gt(rownames_to_stub = TRUE) %>% 
  gt::tab_header(title = "Correlation Table") %>%
  gt_apa_style() %>%
  gt_apa_style(container.width = "65%")  # hacky way of not showing y scroll bar when rendering
```

<div id="bfbpyieqlt" style="overflow-x:auto;overflow-y:auto;width:65%;height:auto;">
<style>html {
  font-family: times, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Helvetica Neue', 'Fira Sans', 'Droid Sans', Arial, sans-serif;
}

#bfbpyieqlt .gt_table {
  display: table;
  border-collapse: collapse;
  margin-left: auto;
  margin-right: auto;
  color: #333333;
  font-size: 12px;
  font-weight: normal;
  font-style: none;
  background-color: #FFFFFF;
  width: auto;
  border-top-style: none;
  border-top-width: 2px;
  border-top-color: #FFFFFF;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #A8A8A8;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
}

#bfbpyieqlt .gt_heading {
  background-color: #FFFFFF;
  text-align: left;
  border-bottom-color: #FFFFFF;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}

#bfbpyieqlt .gt_title {
  color: #333333;
  font-size: 12px;
  font-weight: initial;
  padding-top: 4px;
  padding-bottom: 4px;
  border-bottom-color: #FFFFFF;
  border-bottom-width: 0;
}

#bfbpyieqlt .gt_subtitle {
  color: #333333;
  font-size: 12px;
  font-weight: initial;
  padding-top: 0;
  padding-bottom: 6px;
  border-top-color: #FFFFFF;
  border-top-width: 0;
}

#bfbpyieqlt .gt_bottom_border {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #000000;
}

#bfbpyieqlt .gt_col_headings {
  border-top-style: none;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 1px;
  border-bottom-color: #000000;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}

#bfbpyieqlt .gt_col_heading {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 6px;
  padding-left: 5px;
  padding-right: 5px;
  overflow-x: hidden;
}

#bfbpyieqlt .gt_column_spanner_outer {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  padding-top: 0;
  padding-bottom: 0;
  padding-left: 4px;
  padding-right: 4px;
}

#bfbpyieqlt .gt_column_spanner_outer:first-child {
  padding-left: 0;
}

#bfbpyieqlt .gt_column_spanner_outer:last-child {
  padding-right: 0;
}

#bfbpyieqlt .gt_column_spanner {
  border-bottom-style: solid;
  border-bottom-width: 1px;
  border-bottom-color: #000000;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 5px;
  overflow-x: hidden;
  display: inline-block;
  width: 100%;
}

#bfbpyieqlt .gt_group_heading {
  padding: 8px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-top-style: none;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
}

#bfbpyieqlt .gt_empty_group_heading {
  padding: 0.5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  border-top-style: none;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: middle;
}

#bfbpyieqlt .gt_from_md > :first-child {
  margin-top: 0;
}

#bfbpyieqlt .gt_from_md > :last-child {
  margin-bottom: 0;
}

#bfbpyieqlt .gt_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  margin: 10px;
  border-top-style: none;
  border-top-width: 1px;
  border-top-color: #FFFFFF;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
  overflow-x: hidden;
}

#bfbpyieqlt .gt_stub {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 12px;
}

#bfbpyieqlt .gt_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#bfbpyieqlt .gt_first_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-style: none;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
}

#bfbpyieqlt .gt_grand_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#bfbpyieqlt .gt_first_grand_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-style: none;
  border-top-width: 6px;
  border-top-color: #D3D3D3;
}

#bfbpyieqlt .gt_striped {
  background-color: rgba(128, 128, 128, 0.05);
}

#bfbpyieqlt .gt_table_body {
  border-top-style: solid;
  border-top-width: 1px;
  border-top-color: #000000;
  border-bottom-style: solid;
  border-bottom-width: 1px;
  border-bottom-color: #000000;
}

#bfbpyieqlt .gt_footnotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}

#bfbpyieqlt .gt_footnote {
  margin: 0px;
  font-size: 90%;
  padding: 4px;
}

#bfbpyieqlt .gt_sourcenotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}

#bfbpyieqlt .gt_sourcenote {
  font-size: 90%;
  padding: 4px;
}

#bfbpyieqlt .gt_left {
  text-align: left;
}

#bfbpyieqlt .gt_center {
  text-align: center;
}

#bfbpyieqlt .gt_right {
  text-align: right;
  font-variant-numeric: tabular-nums;
}

#bfbpyieqlt .gt_font_normal {
  font-weight: normal;
}

#bfbpyieqlt .gt_font_bold {
  font-weight: bold;
}

#bfbpyieqlt .gt_font_italic {
  font-style: italic;
}

#bfbpyieqlt .gt_super {
  font-size: 65%;
}

#bfbpyieqlt .gt_footnote_marks {
  font-style: italic;
  font-weight: normal;
  font-size: 65%;
}
</style>
<table class="gt_table">
  <thead class="gt_header">
    <tr>
      <th colspan="4" class="gt_heading gt_title gt_font_normal gt_bottom_border" style>Correlation Table</th>
    </tr>
    
  </thead>
  <thead class="gt_col_headings">
    <tr>
      <th class="gt_col_heading gt_columns_bottom_border gt_left" rowspan="1" colspan="1"></th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1">A</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1">B</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1">y</th>
    </tr>
  </thead>
  <tbody class="gt_table_body">
    <tr><td class="gt_row gt_left gt_stub">A</td>
<td class="gt_row gt_right">1.00</td>
<td class="gt_row gt_right">-0.06</td>
<td class="gt_row gt_right">0.08</td></tr>
    <tr><td class="gt_row gt_left gt_stub">B</td>
<td class="gt_row gt_right">-0.06</td>
<td class="gt_row gt_right">1.00</td>
<td class="gt_row gt_right">0.10</td></tr>
    <tr><td class="gt_row gt_left gt_stub">y</td>
<td class="gt_row gt_right">0.08</td>
<td class="gt_row gt_right">0.10</td>
<td class="gt_row gt_right">1.00</td></tr>
  </tbody>
  
  
</table>
</div>

<br>

##### 3) Check the ANOVA table

There seems to be an interaction effect!

``` r
# Check ANOVA output
aov_fit <- aov(y ~ A + B + A:B, data = data)
gtsummary::tbl_regression(aov_fit) %>% 
  gtsummary::modify_header(sumsq ~ "**Sum Sq**", statistic ~ "**F value**") %>%
  gtsummary::bold_p() %>%
  gtsummary::as_gt() %>%
  gt::tab_header(title = "ANOVA Table") %>%
  gt_apa_style() %>%
  gt_apa_style(container.width = "50%", container.overflow.x = FALSE)  # hacky way of not showing y scroll bar when rendering
```

<div id="dfbcrqxrkh" style="overflow-x:hidden;overflow-y:auto;width:50%;height:auto;">
<style>html {
  font-family: times, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Helvetica Neue', 'Fira Sans', 'Droid Sans', Arial, sans-serif;
}

#dfbcrqxrkh .gt_table {
  display: table;
  border-collapse: collapse;
  margin-left: auto;
  margin-right: auto;
  color: #333333;
  font-size: 12px;
  font-weight: normal;
  font-style: none;
  background-color: #FFFFFF;
  width: auto;
  border-top-style: none;
  border-top-width: 2px;
  border-top-color: #FFFFFF;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #A8A8A8;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
}

#dfbcrqxrkh .gt_heading {
  background-color: #FFFFFF;
  text-align: left;
  border-bottom-color: #FFFFFF;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}

#dfbcrqxrkh .gt_title {
  color: #333333;
  font-size: 12px;
  font-weight: initial;
  padding-top: 4px;
  padding-bottom: 4px;
  border-bottom-color: #FFFFFF;
  border-bottom-width: 0;
}

#dfbcrqxrkh .gt_subtitle {
  color: #333333;
  font-size: 12px;
  font-weight: initial;
  padding-top: 0;
  padding-bottom: 6px;
  border-top-color: #FFFFFF;
  border-top-width: 0;
}

#dfbcrqxrkh .gt_bottom_border {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #000000;
}

#dfbcrqxrkh .gt_col_headings {
  border-top-style: none;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 1px;
  border-bottom-color: #000000;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}

#dfbcrqxrkh .gt_col_heading {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 6px;
  padding-left: 5px;
  padding-right: 5px;
  overflow-x: hidden;
}

#dfbcrqxrkh .gt_column_spanner_outer {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  padding-top: 0;
  padding-bottom: 0;
  padding-left: 4px;
  padding-right: 4px;
}

#dfbcrqxrkh .gt_column_spanner_outer:first-child {
  padding-left: 0;
}

#dfbcrqxrkh .gt_column_spanner_outer:last-child {
  padding-right: 0;
}

#dfbcrqxrkh .gt_column_spanner {
  border-bottom-style: solid;
  border-bottom-width: 1px;
  border-bottom-color: #000000;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 5px;
  overflow-x: hidden;
  display: inline-block;
  width: 100%;
}

#dfbcrqxrkh .gt_group_heading {
  padding: 8px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-top-style: none;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
}

#dfbcrqxrkh .gt_empty_group_heading {
  padding: 0.5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  border-top-style: none;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: middle;
}

#dfbcrqxrkh .gt_from_md > :first-child {
  margin-top: 0;
}

#dfbcrqxrkh .gt_from_md > :last-child {
  margin-bottom: 0;
}

#dfbcrqxrkh .gt_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  margin: 10px;
  border-top-style: none;
  border-top-width: 1px;
  border-top-color: #FFFFFF;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
  overflow-x: hidden;
}

#dfbcrqxrkh .gt_stub {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 12px;
}

#dfbcrqxrkh .gt_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#dfbcrqxrkh .gt_first_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-style: none;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
}

#dfbcrqxrkh .gt_grand_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#dfbcrqxrkh .gt_first_grand_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-style: none;
  border-top-width: 6px;
  border-top-color: #D3D3D3;
}

#dfbcrqxrkh .gt_striped {
  background-color: rgba(128, 128, 128, 0.05);
}

#dfbcrqxrkh .gt_table_body {
  border-top-style: solid;
  border-top-width: 1px;
  border-top-color: #000000;
  border-bottom-style: solid;
  border-bottom-width: 1px;
  border-bottom-color: #000000;
}

#dfbcrqxrkh .gt_footnotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}

#dfbcrqxrkh .gt_footnote {
  margin: 0px;
  font-size: 90%;
  padding: 4px;
}

#dfbcrqxrkh .gt_sourcenotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}

#dfbcrqxrkh .gt_sourcenote {
  font-size: 90%;
  padding: 4px;
}

#dfbcrqxrkh .gt_left {
  text-align: left;
}

#dfbcrqxrkh .gt_center {
  text-align: center;
}

#dfbcrqxrkh .gt_right {
  text-align: right;
  font-variant-numeric: tabular-nums;
}

#dfbcrqxrkh .gt_font_normal {
  font-weight: normal;
}

#dfbcrqxrkh .gt_font_bold {
  font-weight: bold;
}

#dfbcrqxrkh .gt_font_italic {
  font-style: italic;
}

#dfbcrqxrkh .gt_super {
  font-size: 65%;
}

#dfbcrqxrkh .gt_footnote_marks {
  font-style: italic;
  font-weight: normal;
  font-size: 65%;
}
</style>
<table class="gt_table">
  <thead class="gt_header">
    <tr>
      <th colspan="4" class="gt_heading gt_title gt_font_normal gt_bottom_border" style>ANOVA Table</th>
    </tr>
    
  </thead>
  <thead class="gt_col_headings">
    <tr>
      <th class="gt_col_heading gt_columns_bottom_border gt_left" rowspan="1" colspan="1"><strong>Characteristic</strong></th>
      <th class="gt_col_heading gt_columns_bottom_border gt_center" rowspan="1" colspan="1"><strong>Sum Sq</strong></th>
      <th class="gt_col_heading gt_columns_bottom_border gt_center" rowspan="1" colspan="1"><strong>F value</strong></th>
      <th class="gt_col_heading gt_columns_bottom_border gt_center" rowspan="1" colspan="1"><strong>p-value</strong></th>
    </tr>
  </thead>
  <tbody class="gt_table_body">
    <tr><td class="gt_row gt_left">A</td>
<td class="gt_row gt_center">0.492</td>
<td class="gt_row gt_center">1.37</td>
<td class="gt_row gt_center">0.2</td></tr>
    <tr><td class="gt_row gt_left">B</td>
<td class="gt_row gt_center">0.843</td>
<td class="gt_row gt_center">2.35</td>
<td class="gt_row gt_center">0.13</td></tr>
    <tr><td class="gt_row gt_left">A * B</td>
<td class="gt_row gt_center">1.73</td>
<td class="gt_row gt_center">4.82</td>
<td class="gt_row gt_center" style="font-weight: bold;">0.029</td></tr>
  </tbody>
  
  
</table>
</div>

<br>

##### 4) Interaction Plot

Now, I can see the interaction effect.

``` r
# Interaction plot
int_plot1 <- 
  ggplot(data, aes(x = as.factor(A), y = y, group = as.factor(B), shape = as.factor(B))) +
  stat_summary(fun = "mean", geom = "point", size = 4, color = "black") +
  stat_summary(fun = "mean", geom = "line", color = "black") +
  stat_summary(fun.data = "mean_se", geom = "errorbar", width = .1, color = "black") +
  ggtitle("Interaction Plot") +
  xlab("A") + 
  labs(shape = "B", color = "B") +
  theme_apa() 

int_plot1 +
  annotate("rect", xmin = -.2, xmax = 1.1, ymin = -.11, ymax = .13, alpha = .2, color = "blue") +
  annotate("text", x = 0.4, y = 0, label = "When B changes \n from 0 to 1,\n y increases\n very little") +
  annotate("rect", xmin = 1.9, xmax = 3, ymin = -.16, ymax = .77, alpha = .2, color = "blue") +
  annotate("text", x = 2.5, y = .22, label = "When B changes \n from 0 to 1,\n y increases\n quite a lot") 
```

<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-11-1.png" width="672" />

``` r
int_plot2 <- 
  ggplot(data, aes(x = as.factor(A), y = y, group = as.factor(B), shape = as.factor(B))) +
    geom_point(aes(color = as.factor(B)), alpha = .5) +
    stat_summary(fun = "mean", geom = "point", size = 2.2, color = "black") +
    stat_summary(fun = "mean", geom = "line", color = "black") +
    stat_summary(fun.data = "mean_se", geom = "errorbar", width = .1, color = "black") +
    ggtitle("Interaction Plot") +
    xlab("A") + 
    labs(shape = "B", color = "B") +
    theme_apa()

int_plot2
```

<img src="{{< blogdown/postref >}}index.en_files/figure-html/unnamed-chunk-12-1.png" width="672" />

#### II. 3. Artifactual interaction due to categorization

##### 1) Simulate data

Similarly to how we did first time, we start with three continuous standard normal correlated variables. Then we categorize the predictors into dichotomous variables A and B using median split and splitting on the 80th Percentile.

``` r
## 2x2 ANOVA model simulation - artifactual interaction due to categorization
set.seed(42)

# Construct a correlation matrix for two correlated normal variables
rho <- 0
m <- matrix(c(1, 0.4, 0.4, 1), nrow = 2)

# Simulate 200 data points for A and B that are normally distributed 
data <-  MASS::mvrnorm(n = 200, mu = c(0, 0), Sigma = m, empirical = TRUE)
data <- as.data.frame(data)
colnames(data) <- c("A", "B")
data$AB <- data$A * data$B     # interaction term is simple multiplication of A and B

# Simulate outcome y according with OLS equation
beta1 <- .70
beta2 <- .70
beta3 <- .0001
error <- rnorm(200, 0, sqrt(1 - (beta1^2 + beta2^2 + beta3^2)))
data$y <- beta1*data$A + beta2*data$B + beta3*data$AB + error

# Categorize A and B
data$A_med <- cut(data$A, breaks = c(-Inf, quantile(data$A, 0.5), Inf), labels = c("below Median", "above Median")) 
data$B_med <- cut(data$B, breaks = c(-Inf, quantile(data$B, 0.5), Inf), labels = c("below Median", "above Median")) 
data$A_p80 <- cut(data$A, breaks = c(-Inf, quantile(data$A, 0.75), Inf), labels = c("below P80", "above P80")) 
data$B_p80 <- cut(data$B, breaks = c(-Inf, quantile(data$B, 0.75), Inf), labels = c("below P80", "above P80")) 
```

##### 2) Check number of observation by condition with frequency table

``` r
# Check factor table
cond_freq_tab1 <- table(data$A_med, data $B_med) %>% as.data.frame() %>% gt::gt(rownames_to_stub = TRUE) %>% gt_apa_style()
cond_freq_tab2 <- table(data$A_p80, data $B_p80) %>% as.data.frame() %>% gt::gt(rownames_to_stub = TRUE) %>% gt_apa_style()
```

``` r
cond_freq_tab1 %>%
  gt_apa_style(container.width = "50%", container.overflow.x = FALSE)  # hacky way of not showing y scroll bar when rendering
```

<div id="bfbpyieqlt" style="overflow-x:hidden;overflow-y:auto;width:50%;height:auto;">
<style>html {
  font-family: times, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Helvetica Neue', 'Fira Sans', 'Droid Sans', Arial, sans-serif;
}

#bfbpyieqlt .gt_table {
  display: table;
  border-collapse: collapse;
  margin-left: auto;
  margin-right: auto;
  color: #333333;
  font-size: 12px;
  font-weight: normal;
  font-style: none;
  background-color: #FFFFFF;
  width: auto;
  border-top-style: none;
  border-top-width: 2px;
  border-top-color: #FFFFFF;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #A8A8A8;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
}

#bfbpyieqlt .gt_heading {
  background-color: #FFFFFF;
  text-align: left;
  border-bottom-color: #FFFFFF;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}

#bfbpyieqlt .gt_title {
  color: #333333;
  font-size: 12px;
  font-weight: initial;
  padding-top: 4px;
  padding-bottom: 4px;
  border-bottom-color: #FFFFFF;
  border-bottom-width: 0;
}

#bfbpyieqlt .gt_subtitle {
  color: #333333;
  font-size: 12px;
  font-weight: initial;
  padding-top: 0;
  padding-bottom: 6px;
  border-top-color: #FFFFFF;
  border-top-width: 0;
}

#bfbpyieqlt .gt_bottom_border {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #000000;
}

#bfbpyieqlt .gt_col_headings {
  border-top-style: none;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 1px;
  border-bottom-color: #000000;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}

#bfbpyieqlt .gt_col_heading {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 6px;
  padding-left: 5px;
  padding-right: 5px;
  overflow-x: hidden;
}

#bfbpyieqlt .gt_column_spanner_outer {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  padding-top: 0;
  padding-bottom: 0;
  padding-left: 4px;
  padding-right: 4px;
}

#bfbpyieqlt .gt_column_spanner_outer:first-child {
  padding-left: 0;
}

#bfbpyieqlt .gt_column_spanner_outer:last-child {
  padding-right: 0;
}

#bfbpyieqlt .gt_column_spanner {
  border-bottom-style: solid;
  border-bottom-width: 1px;
  border-bottom-color: #000000;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 5px;
  overflow-x: hidden;
  display: inline-block;
  width: 100%;
}

#bfbpyieqlt .gt_group_heading {
  padding: 8px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-top-style: none;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
}

#bfbpyieqlt .gt_empty_group_heading {
  padding: 0.5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  border-top-style: none;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: middle;
}

#bfbpyieqlt .gt_from_md > :first-child {
  margin-top: 0;
}

#bfbpyieqlt .gt_from_md > :last-child {
  margin-bottom: 0;
}

#bfbpyieqlt .gt_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  margin: 10px;
  border-top-style: none;
  border-top-width: 1px;
  border-top-color: #FFFFFF;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
  overflow-x: hidden;
}

#bfbpyieqlt .gt_stub {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 12px;
}

#bfbpyieqlt .gt_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#bfbpyieqlt .gt_first_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-style: none;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
}

#bfbpyieqlt .gt_grand_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#bfbpyieqlt .gt_first_grand_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-style: none;
  border-top-width: 6px;
  border-top-color: #D3D3D3;
}

#bfbpyieqlt .gt_striped {
  background-color: rgba(128, 128, 128, 0.05);
}

#bfbpyieqlt .gt_table_body {
  border-top-style: solid;
  border-top-width: 1px;
  border-top-color: #000000;
  border-bottom-style: solid;
  border-bottom-width: 1px;
  border-bottom-color: #000000;
}

#bfbpyieqlt .gt_footnotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}

#bfbpyieqlt .gt_footnote {
  margin: 0px;
  font-size: 90%;
  padding: 4px;
}

#bfbpyieqlt .gt_sourcenotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}

#bfbpyieqlt .gt_sourcenote {
  font-size: 90%;
  padding: 4px;
}

#bfbpyieqlt .gt_left {
  text-align: left;
}

#bfbpyieqlt .gt_center {
  text-align: center;
}

#bfbpyieqlt .gt_right {
  text-align: right;
  font-variant-numeric: tabular-nums;
}

#bfbpyieqlt .gt_font_normal {
  font-weight: normal;
}

#bfbpyieqlt .gt_font_bold {
  font-weight: bold;
}

#bfbpyieqlt .gt_font_italic {
  font-style: italic;
}

#bfbpyieqlt .gt_super {
  font-size: 65%;
}

#bfbpyieqlt .gt_footnote_marks {
  font-style: italic;
  font-weight: normal;
  font-size: 65%;
}
</style>
<table class="gt_table">
  
  <thead class="gt_col_headings">
    <tr>
      <th class="gt_col_heading gt_columns_bottom_border gt_left" rowspan="1" colspan="1"></th>
      <th class="gt_col_heading gt_columns_bottom_border gt_center" rowspan="1" colspan="1">Var1</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_center" rowspan="1" colspan="1">Var2</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1">Freq</th>
    </tr>
  </thead>
  <tbody class="gt_table_body">
    <tr><td class="gt_row gt_left gt_stub">1</td>
<td class="gt_row gt_center">below Median</td>
<td class="gt_row gt_center">below Median</td>
<td class="gt_row gt_right">62</td></tr>
    <tr><td class="gt_row gt_left gt_stub">2</td>
<td class="gt_row gt_center">above Median</td>
<td class="gt_row gt_center">below Median</td>
<td class="gt_row gt_right">38</td></tr>
    <tr><td class="gt_row gt_left gt_stub">3</td>
<td class="gt_row gt_center">below Median</td>
<td class="gt_row gt_center">above Median</td>
<td class="gt_row gt_right">38</td></tr>
    <tr><td class="gt_row gt_left gt_stub">4</td>
<td class="gt_row gt_center">above Median</td>
<td class="gt_row gt_center">above Median</td>
<td class="gt_row gt_right">62</td></tr>
  </tbody>
  
  
</table>
</div>

<br>

``` r
cond_freq_tab2 %>%
  gt_apa_style(container.width = "50%", container.overflow.x = FALSE)  # hacky way of not showing y scroll bar when rendering
```

<div id="dfbcrqxrkh" style="overflow-x:hidden;overflow-y:auto;width:50%;height:auto;">
<style>html {
  font-family: times, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Helvetica Neue', 'Fira Sans', 'Droid Sans', Arial, sans-serif;
}

#dfbcrqxrkh .gt_table {
  display: table;
  border-collapse: collapse;
  margin-left: auto;
  margin-right: auto;
  color: #333333;
  font-size: 12px;
  font-weight: normal;
  font-style: none;
  background-color: #FFFFFF;
  width: auto;
  border-top-style: none;
  border-top-width: 2px;
  border-top-color: #FFFFFF;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #A8A8A8;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
}

#dfbcrqxrkh .gt_heading {
  background-color: #FFFFFF;
  text-align: left;
  border-bottom-color: #FFFFFF;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}

#dfbcrqxrkh .gt_title {
  color: #333333;
  font-size: 12px;
  font-weight: initial;
  padding-top: 4px;
  padding-bottom: 4px;
  border-bottom-color: #FFFFFF;
  border-bottom-width: 0;
}

#dfbcrqxrkh .gt_subtitle {
  color: #333333;
  font-size: 12px;
  font-weight: initial;
  padding-top: 0;
  padding-bottom: 6px;
  border-top-color: #FFFFFF;
  border-top-width: 0;
}

#dfbcrqxrkh .gt_bottom_border {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #000000;
}

#dfbcrqxrkh .gt_col_headings {
  border-top-style: none;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 1px;
  border-bottom-color: #000000;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}

#dfbcrqxrkh .gt_col_heading {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 6px;
  padding-left: 5px;
  padding-right: 5px;
  overflow-x: hidden;
}

#dfbcrqxrkh .gt_column_spanner_outer {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  padding-top: 0;
  padding-bottom: 0;
  padding-left: 4px;
  padding-right: 4px;
}

#dfbcrqxrkh .gt_column_spanner_outer:first-child {
  padding-left: 0;
}

#dfbcrqxrkh .gt_column_spanner_outer:last-child {
  padding-right: 0;
}

#dfbcrqxrkh .gt_column_spanner {
  border-bottom-style: solid;
  border-bottom-width: 1px;
  border-bottom-color: #000000;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 5px;
  overflow-x: hidden;
  display: inline-block;
  width: 100%;
}

#dfbcrqxrkh .gt_group_heading {
  padding: 8px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-top-style: none;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
}

#dfbcrqxrkh .gt_empty_group_heading {
  padding: 0.5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  border-top-style: none;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: middle;
}

#dfbcrqxrkh .gt_from_md > :first-child {
  margin-top: 0;
}

#dfbcrqxrkh .gt_from_md > :last-child {
  margin-bottom: 0;
}

#dfbcrqxrkh .gt_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  margin: 10px;
  border-top-style: none;
  border-top-width: 1px;
  border-top-color: #FFFFFF;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
  overflow-x: hidden;
}

#dfbcrqxrkh .gt_stub {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 12px;
}

#dfbcrqxrkh .gt_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#dfbcrqxrkh .gt_first_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-style: none;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
}

#dfbcrqxrkh .gt_grand_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#dfbcrqxrkh .gt_first_grand_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-style: none;
  border-top-width: 6px;
  border-top-color: #D3D3D3;
}

#dfbcrqxrkh .gt_striped {
  background-color: rgba(128, 128, 128, 0.05);
}

#dfbcrqxrkh .gt_table_body {
  border-top-style: solid;
  border-top-width: 1px;
  border-top-color: #000000;
  border-bottom-style: solid;
  border-bottom-width: 1px;
  border-bottom-color: #000000;
}

#dfbcrqxrkh .gt_footnotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}

#dfbcrqxrkh .gt_footnote {
  margin: 0px;
  font-size: 90%;
  padding: 4px;
}

#dfbcrqxrkh .gt_sourcenotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}

#dfbcrqxrkh .gt_sourcenote {
  font-size: 90%;
  padding: 4px;
}

#dfbcrqxrkh .gt_left {
  text-align: left;
}

#dfbcrqxrkh .gt_center {
  text-align: center;
}

#dfbcrqxrkh .gt_right {
  text-align: right;
  font-variant-numeric: tabular-nums;
}

#dfbcrqxrkh .gt_font_normal {
  font-weight: normal;
}

#dfbcrqxrkh .gt_font_bold {
  font-weight: bold;
}

#dfbcrqxrkh .gt_font_italic {
  font-style: italic;
}

#dfbcrqxrkh .gt_super {
  font-size: 65%;
}

#dfbcrqxrkh .gt_footnote_marks {
  font-style: italic;
  font-weight: normal;
  font-size: 65%;
}
</style>
<table class="gt_table">
  
  <thead class="gt_col_headings">
    <tr>
      <th class="gt_col_heading gt_columns_bottom_border gt_left" rowspan="1" colspan="1"></th>
      <th class="gt_col_heading gt_columns_bottom_border gt_center" rowspan="1" colspan="1">Var1</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_center" rowspan="1" colspan="1">Var2</th>
      <th class="gt_col_heading gt_columns_bottom_border gt_right" rowspan="1" colspan="1">Freq</th>
    </tr>
  </thead>
  <tbody class="gt_table_body">
    <tr><td class="gt_row gt_left gt_stub">1</td>
<td class="gt_row gt_center">below P80</td>
<td class="gt_row gt_center">below P80</td>
<td class="gt_row gt_right">122</td></tr>
    <tr><td class="gt_row gt_left gt_stub">2</td>
<td class="gt_row gt_center">above P80</td>
<td class="gt_row gt_center">below P80</td>
<td class="gt_row gt_right">28</td></tr>
    <tr><td class="gt_row gt_left gt_stub">3</td>
<td class="gt_row gt_center">below P80</td>
<td class="gt_row gt_center">above P80</td>
<td class="gt_row gt_right">28</td></tr>
    <tr><td class="gt_row gt_left gt_stub">4</td>
<td class="gt_row gt_center">above P80</td>
<td class="gt_row gt_center">above P80</td>
<td class="gt_row gt_right">22</td></tr>
  </tbody>
  
  
</table>
</div>

<br>

##### 3) Check the ANOVA table

It is the vanishing interaction effect!

``` r
# Check ANOVA output
aov_fit_med <- aov(y ~ A_med + B_med + A_med:B_med, data = data)
aov_fit_p80 <- aov(y ~ A_p80 + B_p80 + A_p80:B_p80, data = data)

gt_aov_med <- gtsummary::tbl_regression(aov_fit_med) %>% 
  gtsummary::modify_header(sumsq ~ "**Sum Sq**", statistic ~ "**F value**") %>%
  gtsummary::bold_p()
gt_aov_p80 <- gtsummary::tbl_regression(aov_fit_p80) %>% 
  gtsummary::modify_header(sumsq ~ "**Sum Sq**", statistic ~ "**F value**") %>%
  gtsummary::bold_p()

gtsummary::tbl_merge(list(gt_aov_med, gt_aov_p80), tab_spanner = c("**Median Split**", "**P80 Split**")) %>% 
  gtsummary::as_gt() %>% 
  gt_apa_style() %>%
  gt_apa_style(container.width = "95%", container.overflow.x = FALSE)  # hacky way of not showing y scroll bar when rendering
```

<div id="gutvwxaxsp" style="overflow-x:hidden;overflow-y:auto;width:95%;height:auto;">
<style>html {
  font-family: times, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Helvetica Neue', 'Fira Sans', 'Droid Sans', Arial, sans-serif;
}

#gutvwxaxsp .gt_table {
  display: table;
  border-collapse: collapse;
  margin-left: auto;
  margin-right: auto;
  color: #333333;
  font-size: 12px;
  font-weight: normal;
  font-style: none;
  background-color: #FFFFFF;
  width: auto;
  border-top-style: none;
  border-top-width: 2px;
  border-top-color: #FFFFFF;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #A8A8A8;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
}

#gutvwxaxsp .gt_heading {
  background-color: #FFFFFF;
  text-align: left;
  border-bottom-color: #FFFFFF;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}

#gutvwxaxsp .gt_title {
  color: #333333;
  font-size: 12px;
  font-weight: initial;
  padding-top: 4px;
  padding-bottom: 4px;
  border-bottom-color: #FFFFFF;
  border-bottom-width: 0;
}

#gutvwxaxsp .gt_subtitle {
  color: #333333;
  font-size: 12px;
  font-weight: initial;
  padding-top: 0;
  padding-bottom: 6px;
  border-top-color: #FFFFFF;
  border-top-width: 0;
}

#gutvwxaxsp .gt_bottom_border {
  border-bottom-style: solid;
  border-bottom-width: 2px;
  border-bottom-color: #000000;
}

#gutvwxaxsp .gt_col_headings {
  border-top-style: none;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: solid;
  border-bottom-width: 1px;
  border-bottom-color: #000000;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
}

#gutvwxaxsp .gt_col_heading {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 6px;
  padding-left: 5px;
  padding-right: 5px;
  overflow-x: hidden;
}

#gutvwxaxsp .gt_column_spanner_outer {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: normal;
  text-transform: inherit;
  padding-top: 0;
  padding-bottom: 0;
  padding-left: 4px;
  padding-right: 4px;
}

#gutvwxaxsp .gt_column_spanner_outer:first-child {
  padding-left: 0;
}

#gutvwxaxsp .gt_column_spanner_outer:last-child {
  padding-right: 0;
}

#gutvwxaxsp .gt_column_spanner {
  border-bottom-style: solid;
  border-bottom-width: 1px;
  border-bottom-color: #000000;
  vertical-align: bottom;
  padding-top: 5px;
  padding-bottom: 5px;
  overflow-x: hidden;
  display: inline-block;
  width: 100%;
}

#gutvwxaxsp .gt_group_heading {
  padding: 8px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-top-style: none;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
}

#gutvwxaxsp .gt_empty_group_heading {
  padding: 0.5px;
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  border-top-style: none;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  vertical-align: middle;
}

#gutvwxaxsp .gt_from_md > :first-child {
  margin-top: 0;
}

#gutvwxaxsp .gt_from_md > :last-child {
  margin-bottom: 0;
}

#gutvwxaxsp .gt_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  margin: 10px;
  border-top-style: none;
  border-top-width: 1px;
  border-top-color: #FFFFFF;
  border-left-style: none;
  border-left-width: 1px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 1px;
  border-right-color: #D3D3D3;
  vertical-align: middle;
  overflow-x: hidden;
}

#gutvwxaxsp .gt_stub {
  color: #333333;
  background-color: #FFFFFF;
  font-size: 100%;
  font-weight: initial;
  text-transform: inherit;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
  padding-left: 12px;
}

#gutvwxaxsp .gt_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#gutvwxaxsp .gt_first_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-style: none;
  border-top-width: 2px;
  border-top-color: #D3D3D3;
}

#gutvwxaxsp .gt_grand_summary_row {
  color: #333333;
  background-color: #FFFFFF;
  text-transform: inherit;
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
}

#gutvwxaxsp .gt_first_grand_summary_row {
  padding-top: 8px;
  padding-bottom: 8px;
  padding-left: 5px;
  padding-right: 5px;
  border-top-style: none;
  border-top-width: 6px;
  border-top-color: #D3D3D3;
}

#gutvwxaxsp .gt_striped {
  background-color: rgba(128, 128, 128, 0.05);
}

#gutvwxaxsp .gt_table_body {
  border-top-style: solid;
  border-top-width: 1px;
  border-top-color: #000000;
  border-bottom-style: solid;
  border-bottom-width: 1px;
  border-bottom-color: #000000;
}

#gutvwxaxsp .gt_footnotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}

#gutvwxaxsp .gt_footnote {
  margin: 0px;
  font-size: 90%;
  padding: 4px;
}

#gutvwxaxsp .gt_sourcenotes {
  color: #333333;
  background-color: #FFFFFF;
  border-bottom-style: none;
  border-bottom-width: 2px;
  border-bottom-color: #D3D3D3;
  border-left-style: none;
  border-left-width: 2px;
  border-left-color: #D3D3D3;
  border-right-style: none;
  border-right-width: 2px;
  border-right-color: #D3D3D3;
}

#gutvwxaxsp .gt_sourcenote {
  font-size: 90%;
  padding: 4px;
}

#gutvwxaxsp .gt_left {
  text-align: left;
}

#gutvwxaxsp .gt_center {
  text-align: center;
}

#gutvwxaxsp .gt_right {
  text-align: right;
  font-variant-numeric: tabular-nums;
}

#gutvwxaxsp .gt_font_normal {
  font-weight: normal;
}

#gutvwxaxsp .gt_font_bold {
  font-weight: bold;
}

#gutvwxaxsp .gt_font_italic {
  font-style: italic;
}

#gutvwxaxsp .gt_super {
  font-size: 65%;
}

#gutvwxaxsp .gt_footnote_marks {
  font-style: italic;
  font-weight: normal;
  font-size: 65%;
}
</style>
<table class="gt_table">
  
  <thead class="gt_col_headings">
    <tr>
      <th class="gt_col_heading gt_columns_bottom_border gt_left" rowspan="2" colspan="1"><strong>Characteristic</strong></th>
      <th class="gt_center gt_columns_top_border gt_column_spanner_outer" rowspan="1" colspan="3">
        <span class="gt_column_spanner"><strong>Median Split</strong></span>
      </th>
      <th class="gt_center gt_columns_top_border gt_column_spanner_outer" rowspan="1" colspan="3">
        <span class="gt_column_spanner"><strong>P80 Split</strong></span>
      </th>
    </tr>
    <tr>
      <th class="gt_col_heading gt_columns_bottom_border gt_center" rowspan="1" colspan="1"><strong>Sum Sq</strong></th>
      <th class="gt_col_heading gt_columns_bottom_border gt_center" rowspan="1" colspan="1"><strong>F value</strong></th>
      <th class="gt_col_heading gt_columns_bottom_border gt_center" rowspan="1" colspan="1"><strong>p-value</strong></th>
      <th class="gt_col_heading gt_columns_bottom_border gt_center" rowspan="1" colspan="1"><strong>Sum Sq</strong></th>
      <th class="gt_col_heading gt_columns_bottom_border gt_center" rowspan="1" colspan="1"><strong>F value</strong></th>
      <th class="gt_col_heading gt_columns_bottom_border gt_center" rowspan="1" colspan="1"><strong>p-value</strong></th>
    </tr>
  </thead>
  <tbody class="gt_table_body">
    <tr><td class="gt_row gt_left">A_med</td>
<td class="gt_row gt_center">122</td>
<td class="gt_row gt_center">258</td>
<td class="gt_row gt_center" style="font-weight: bold;"><0.001</td>
<td class="gt_row gt_center"></td>
<td class="gt_row gt_center"></td>
<td class="gt_row gt_center"></td></tr>
    <tr><td class="gt_row gt_left">B_med</td>
<td class="gt_row gt_center">68.5</td>
<td class="gt_row gt_center">145</td>
<td class="gt_row gt_center" style="font-weight: bold;"><0.001</td>
<td class="gt_row gt_center"></td>
<td class="gt_row gt_center"></td>
<td class="gt_row gt_center"></td></tr>
    <tr><td class="gt_row gt_left">A_med * B_med</td>
<td class="gt_row gt_center">0.044</td>
<td class="gt_row gt_center">0.093</td>
<td class="gt_row gt_center">0.8</td>
<td class="gt_row gt_center"></td>
<td class="gt_row gt_center"></td>
<td class="gt_row gt_center"></td></tr>
    <tr><td class="gt_row gt_left">A_p80</td>
<td class="gt_row gt_center"></td>
<td class="gt_row gt_center"></td>
<td class="gt_row gt_center"></td>
<td class="gt_row gt_center">92.3</td>
<td class="gt_row gt_center">162</td>
<td class="gt_row gt_center" style="font-weight: bold;"><0.001</td></tr>
    <tr><td class="gt_row gt_left">B_p80</td>
<td class="gt_row gt_center"></td>
<td class="gt_row gt_center"></td>
<td class="gt_row gt_center"></td>
<td class="gt_row gt_center">76.2</td>
<td class="gt_row gt_center">134</td>
<td class="gt_row gt_center" style="font-weight: bold;"><0.001</td></tr>
    <tr><td class="gt_row gt_left">A_p80 * B_p80</td>
<td class="gt_row gt_center"></td>
<td class="gt_row gt_center"></td>
<td class="gt_row gt_center"></td>
<td class="gt_row gt_center">2.74</td>
<td class="gt_row gt_center">4.81</td>
<td class="gt_row gt_center" style="font-weight: bold;">0.029</td></tr>
  </tbody>
  
  
</table>
</div>

<br>
<br>
<br>

### III. Empirical and rational answer

Until this point we have simplified our discussion to only deal with **statistical interaction**. Statistical interactions are only a deviation from a specified model form. Here, we have only presented a *multipicative* statistical interaction, but many such functional forms could exist in the wild (e.g. *sin(x1 + x2)*). Also, nonlinear effects of individual predictors can masquerade as interaction effects between correlated predictors (Belzak & Bauer, 2019). Data features like multicollinearity of predictors (Sithisarankul et al., 1997) may lead to artificial interaction effects (I would call these ***spurious***). Statistical procedures like categorization of continuous predictors (Thoresen, 2019) may lead to artificial interaction effects (I would call these ***artifactual***).

Most outcomes are not caused by a single factor in isolation, but by multiple in combinations. Nevertheless, in the real world we deal with unreasonable numbers of such factor and usually only a small proportion of the factors will have significant effects. This fact has been called the effect sparsity principle (Box & Meyer, 1986). Just as two planets will line up with the moon more frequently than three planets, main effects are more likely than two-factor interactions, and two-factor interactions are more likely than three-factor interactions etc. This phenomena has been termed “the hierarchical ordering principle” (Wu & Hamada, 2000).

Unfortunately, answering the question only at the level of **statistical interaction** leaves out the true structure of world and our understanding void of the of the causal underpinnings of the correlations and interactions we sometimes detect and sometimes don’t.

<br>

#### III. 1. Counterfactual reasoning

Similarly to Judea Pearl’s genius **Ladder of Causation**, I like to think about a hierarchy of interaction: **statistical interaction -&gt; causal interaction -&gt; mechanistic interaction**. To move from probabilistic conclusions to causal one, some assumptions have to be met (e.g. no unmeasured confounding, correctly specified model etc.), and similarly to move from the causal to the mechanistic further conditions have to be met. Without going into detail, a statistical interaction may not correspond to a causal one, while a causal interaction may not map to any one mechanistic interaction.

Unfortunately, a blog post cannot familiarize readers with causal inference, the counterfactual framework or the directed acyclic graphs (DAG) used as common representation method. However, I will try to shed more light on a common misunderstanding from conflating effect modification with causal interaction. The following simplified explanations heavily rely on work of Bours (2021), VanderWeele & Robins (2007), VanderWeele (2009), and works of Herman and Robins (2009). Readers are advised to read the primary sources.

<br>

**Effect modification** = the causal effect on outcome ***D*** of one factor ***E*** differs across levels of a second factor ***Q***. This is akin to typical *effect heterogeneity* in epidemiology and *moderation* in social sciences, in that relationship between E and Q is asymmetric: the focus is on the causal effect of E on D that only varies across strata of Q (Q may be an unchanging natural category or it may even have no causal effect on D at all).

**Causal interaction** = the joint causal effect of two factors ***E*** and ***Q*** on outcome ***D*** differs from their expected combined effect. Causal interaction is essentially a symmetric concept - both factors have an equal status in the definition of interaction and if manipulation of any is possible it will result in outcome change.

VanderWeele and Robins (2007) showed that a variable Q could serve as an effect modifier for the effect of E on D even if Q had no causal effect on D. This happens in effect modification by common cause or effect modification by proxy. But more importantly, VanderWeele (2009) gives the following two examples of interaction

<br>

##### Effect modification without causal interaction

<figure>

<img src="DAG1.jpeg" width="427" height="327"/>

<figcaption>

Effect modification by Q of the effect of E on D without causal interaction between the effects of E and Q on D (VanderWeele, 2009).

</figcaption>
</figure>

“E denotes some drug in a randomized trial, D denotes hypertension, X denotes a person’s genotype, and Q denotes the person’s hair color. In this example, Q might serve as an effect modifier for the effect of E on D since Q is a proxy for X. Q has no effect whatsoever on D. Interventions on Q do nothing to D, so there can be no interaction between E and Q” (VanderWeele, 2009).

##### Causal interaction without effect modification

<figure>

<img src="DAG2.jpeg" width="427" height="327"/>

<figcaption>

Causal interaction between the effects of E and Q on D without effect modification by Q of the effect of E on D. (VanderWeele, 2009).

</figcaption>
</figure>

“E denotes a drug in a randomized trial for weight loss for obese children, D denotes the final weight of a child 6 months after the trial completion, X denotes the sugar intake in a child’s diet, and Q denotes some measure of exercise. Sugar intake might, for example, affect exercise by making children more hyperactive. Suppose further that the effects of the diet drug E and exercise Q interact. In other words, if we were to intervene to give children the drug and also to force exercise, then the effect of these 2 interventions together, compared with the baseline of no drug and no exercise, would be greater than the sum of the effects of each intervention considered separately. We would then say that there is an interaction between the effects of E and Q on D. However, it does not follow from this that Q is an effect modifier for the effect of E on D. Suppose that sugar intake had 2 effects on weight; first, high levels of sugar intake within the diet will likely increase weight gain; second, sugar intake might give children more energy, making them hyperactive and thus more likely to exercise, and thereby lowering their weight through exercise. It is furthermore possible that the indirect effect of sugar intake that lowers weight through exercise essentially cancels out the direct effect of sugar intake which increases weight” (VanderWeele, 2009).

<br>
<br>

#### References

-   Belzak, W. C., & Bauer, D. J. (2019). Interaction effects may actually be nonlinear effects in disguise: A review of the problem and potential solutions. Addictive behaviors, 94, 99-108.
-   Sithisarankul, P., Weaver, V. M., Diener-West, M., & Strickland, P. T. (1997). Multicollinearity may lead to artificial interaction: an example from a cross sectional study of biomarkers. The Southeast Asian journal of tropical medicine and public health, 28(2), 404-409.
-   Thoresen, M. (2019). Spurious interaction as a result of categorization. BMC medical research methodology, 19(1), 1-8.
-   Box, G. E. P. and Meyer, R. D. (1986). An analysis for unreplicated fractional factorials. Technometrics, 28, 11-18.
-   Wu, C. F. J. and Hamada, M. (2000). Experiments-Planning, Analysis, and Parameter Design Optimization. New York: John Wiley & Sons.
-   Bours, M. J. (2021). Tutorial: A nontechnical explanation of the counterfactual definition of effect modification and interaction. Journal of clinical epidemiology, 134, 113-124.
-   VanderWeele, T. J., & Robins, J. M. (2007). Four types of effect modification: a classification based on directed acyclic graphs. Epidemiology, 18(5), 561-568.
-   VanderWeele, T. J. (2009). On the distinction between interaction and effect modification. Epidemiology, 20(6), 863-871.
-   Hernán M.A. & Robins J.M. (2020). Causal Inference: What If. Boca Raton: Chapman & Hall/CRC.

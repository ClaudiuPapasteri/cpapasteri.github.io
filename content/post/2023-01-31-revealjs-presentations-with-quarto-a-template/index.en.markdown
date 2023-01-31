---
title: 'Revealjs presentations with Quarto: a template'
author: Claudiu C. Papasteri
date: '2023-01-31'
slug: revealjs-presentations-with-quarto-a-template
categories:
  - R
tags:
  - R
  - Quarto
  - revealjs
  - template
  - boilerplate
subtitle: ''
summary: ''
authors: []
lastmod: '2023-01-31T09:32:33+02:00'
featured: yes
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
---

&nbsp;

#### Introduction

I love `blogdown`, `bookdown`, `packagedown` and other downs (`xaringan` not so much). But the new star is Quarto.

The name "Quarto" allegedly comes from the format type used to print the Sibyllenbuch, believed to have been printed by Johannes Gutenberg in 1452--53.

Quarto is a language agnostic CTI tool built on Pandoc that can render our markdown into almost anything (e.g. HTML, PDF, MS Word, Revealjs, ePub, etc.). Just change the extension "`.rmd`" to "`.qmd`" and it will run (*although it is better to fully jump on the Quarto train an embrace the much more elegant settings declarations and chunk options*).

There are many tutorials about Quarto and this will not be one of them. I will just point you to a [**boilerplate reveal.js presentation**](https://claudiupapasteri.github.io/quarto-slide/e-quarto-slide.html#/) ([code in Github](https://github.com/ClaudiuPapasteri/quarto-slide)) and add a few tips that I consider very important.

&nbsp;

<iframe src="https://claudiupapasteri.github.io/quarto-slide/e-quarto-slide.html#/" width="672" height="400px" data-external="1"></iframe>

&nbsp;

#### Extensions

What I use:

[Collapse Callout Blocks](https://github.com/shafayetShafee/collapse-callout)

[Font Awesome Extension for Quarto](https://github.com/quarto-ext/fontawesome)

[QR Codes](https://github.com/jmbuhr/quarto-qrcode)

<br> To use extensions:

1.  Install from terminal

-   `quarto install extension repo/quartoextension`

2.  Add extension to the project you use it in

-   `quarto add repo/quartoextension`

&nbsp;

#### Print to PDF:

1.  Toggle into Print View using the E key (or using the Navigation Menu).

2.  Open the in-browser print dialog (CTRL/CMD+P).

3.  Change the Destination setting to Save as PDF.

4.  Change the Layout to Landscape.

5.  Change the Margins to None.

6.  Enable the Background graphics option.

7.  Click Save

&nbsp;

#### Notes

Github Pages doesn't render the presentation correctly if it is not self-contained HTML ([looks like this](https://claudiupapasteri.github.io/quarto-slide/quarto-slide.html#/)). To obtain a self-contained HTML we use `embed-resources: true` but this has some drawbacks.

Embedding video using Quarto default functionality fails when using `embed-resources: true`([fixed in Quarto 1.3](https://github.com/quarto-dev/quarto-cli/issues/3310), but current version I use is version 1.2.269).External videos (e.g. youtube) embedding work with `iframe` and `data-external="1"`.

Chalkboard plugin conflicts with `embed-resources: true` so it must be disabled.

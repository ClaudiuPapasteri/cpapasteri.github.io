---
title: Updating R and RStudio and avoiding dependency hell of updating to new major
  version
author: Claudiu C. Papasteri
date: '2021-09-09'
slug: updating-r-and-rstudio-and-avoiding-dependency-hell-of-updating-to-new-major-version
categories:
  - R
tags:
  - update
  - R
  - Rstudio
  - packages
subtitle: ''
summary: ''
authors: []
lastmod: '2021-09-09T14:14:38+03:00'
featured: yes
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
---

### Updating R (for Windows)

The most straightforward way to update R on Windows is to use the `installr` package. `installr` can also be used to install also 'Rtools', 'RStudio' and 'Git'.

Keep in mind that it is not recommended to use `installr` from RStudio, so use R instead. Some more details on updating R with `installr` can be found [here](https://www.r-statistics.com/2013/03/updating-r-from-r-on-windows-using-the-installr-package/).


```r
## Update R for Windows - best from R, do not use RStudio  
# Load or, if necessary, install & load {installr} package
if(!require(installr)) {
  install.packages("installr"); 
  require(installr)
} 
```

After installing/loading `installr` you should chose your preferred method for updating: non-interactive or interactive.

#### For non-interactive full update sequence use:

To non-interactively install, move packages from old version to new version, update packages and quit R run this:


```r
# For non-interactive full update sequence use:
installr::updateR(browse_news = F,
                  install_R = T, 
                  copy_packages = T, 
                  copy_site_files = F, 
                  keep_old_packages = F, 
                  update_packages = T, 
                  start_new_R = F,
                  quit_R = T
) # install, move, update.package, quit R.
```

#### For interactive pop-up guided full update sequence use:

This will start the updating process of your R installation. It will check for newer versions, and if one is available, will guide you through the decisions you'd need to make. Once the installation is done, you should press "any-key", and the function will proceed with copying all of your packages from your old (well, current) R installation, into your newer R installation.


```r
# For interactive pop-up guided full update sequence use: 
installr::updateR()
```

##### Sequential steps by component functions

You can also run each step of the sequence by calling the component functions of the `installr::updateR()` function:


```r
installr::check.for.updates.R() # tells you if there is a new version of R or not.
installr::install.R() # download and run the latest R installer
installr::copy.packages.between.libraries() # copy your packages to the newest R installation from the one version before it (if ask=T, it will ask you between which two versions to perform the copying)
```

### Update RStudio

#### For interactive install from inside RStudio

1.  Open RStudio.

2.  Navigate to Help Menu.

3.  Check for updates.

4.  If any new update is available, click on Install.

#### For non-interactive install from R

We can use `installr` package to install the new RStudio.


```r
# Install RStudio
installr::install.RStudio()
```

### Updating Packages

If you followed the steps above, you would have moved (i.e. cut/paste) the packages from older version to new version by using `installr`. Now, if you transition beween minor version of R, you just need to update the packages:


```r
# Update packages
update.packages(ask = FALSE)  #  update all packages except those installed from other sources (e.g. with devtools::install_github())
```

Unfortunately, if you are updating between major version (e.g. R version 3.0 to R version 4.0) updating won't help because you will get the infamous error:

> Package 'XXX' was installed before R 4.0.0: please re-install it

This means that you need to re-install all of your packages, which may take some time. I will share the sequence I use, but keep in mind that it uses `install.packages()` and is sub-optimal because some packages that are frequent dependencies of others get installed more than once (a single version will remain though).

1)  **You should know where your packages live**


```r
# Paths to installed packages
.libPaths()
```

2)  **Detach all non-core packages**

You can detach packages from console or RStudio, but the easiest and safest way is to use `pacman`.


```r
# Restart R
.rs.restartR()

# Clear workspace
rm(list = ls(all = TRUE))

# Detach all packages
# Load or, if necessary, install & load {pacman} package
if(!require(pacman)) {
  install.packages("pacman"); 
  require(pacman)
} 

pacman::p_unload(pacman::p_loaded(), character.only = TRUE)
```

If needed there is a package called `nothing` that when loaded will unload all of the loaded packages. You can install it from GitHub and use it like this:


```r
devtools::install_github("romainfrancois/nothing")   
require(nothing, quietly = TRUE)
loadedNamespaces()  # only package {base} should remain
```

3)  **Check what packages remain loaded**

If you followed along, only core packages should remain loaded.


```r
# Check what packages remain loaded
loadedNamespaces()
```

4)  **Re-install all previously installed packages**

This will take a while ...


```r
# Re-install packages
to_install <- unname(installed.packages()[, "Package"])
for(lib in to_install) install.packages(lib, dependencies = TRUE)
```

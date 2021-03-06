---
title: How to install R, R packages, RStudio and RStudio Server on Ubuntu Linux
author: Claudiu C. Papasteri
date: '2022-06-04'
slug: install-r-rstudio-rstudioserver-ubuntu-linux
categories:
  - R
  - Ubuntu
tags:
  - R
  - Rstudio
  - RStudio Server
  - Ubuntu
  - Linux
subtitle: ''
summary: ''
authors: []
lastmod: '2022-06-04T11:28:55+03:00'
featured: yes
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
---

### Install R (`r-base`)

##### 1. Install helper software `software-properties-common` and `dirmngr`

-   `software-properties-common` for managing the repositories that you install software from.

-   `dirmngr` for managing and downloading certificate revocation lists and for downloading the certificates themselves.


```r
# update indices
sudo apt update -qq
# install two helper packages we need
sudo apt install --no-install-recommends software-properties-common dirmngr
```

##### 2. Set repository

R (`r-base`) can be installed by simply running `apt install`, but the version of R in the default repository is usually not up-to-date. So we are going to tell `apt install` to obtain the latest version from a CRAN repository.

To do this we can make an entry in `/etc/apt/sources.list` file by running `sudo nano /etc/apt/sources.list` and pasting in `deb https://<my.favorite.cran.mirror>/bin/linux/ubuntu <code-name-adjective>-cran40/` (where *\<my.favorite.cran.mirror\>* may be `https://cloud.r-project.org` and *`<code-name-adjective>`* is the Ubuntu release code name adjective). After, we only need to add the key so that `apt` can perform signature checking of the Release File for the added repository to verify its authenticity. The CRAN repository for Ubuntu is signed with the key of "Michael Rutter [marutter\@gmail.com](mailto:marutter@gmail.com){.email}".

Fortunately, all of this can be done by running the following:


```r
# add the signing key (by Michael Rutter) for these repos
# To verify key, run gpg --show-keys /etc/apt/trusted.gpg.d/cran_ubuntu_key.asc 
# Fingerprint: E298A3A825C0D65DFD57CBB651716619E084DAB9
wget -qO- https://cloud.r-project.org/bin/linux/ubuntu/marutter_pubkey.asc | sudo tee -a /etc/apt/trusted.gpg.d/cran_ubuntu_key.asc
# add the R 4.0 repo from CRAN -- adjust 'focal' to 'groovy' or 'bionic' as needed
sudo add-apt-repository "deb https://cloud.r-project.org/bin/linux/ubuntu $(lsb_release -cs)-cran40/"
```

We use `lsb_release -cs` to access which Ubuntu flavor the machine is running. For this example, I am running Impish Indri (21.10, amd64 only), so I could have typed `impish` instead of `$(lsb_release -cs)`.  


##### 3. Install `r-base`

Install R and its dependencies by running:


```r
sudo apt install --no-install-recommends r-base
```

Up until this point, everything we done is the step-by-step installation provided by [official CRAN Brief Instructions](http://cran.rstudio.com/bin/linux/ubuntu/) (it is a good idea to also check the [full README](https://cran.rstudio.com/bin/linux/ubuntu/fullREADME.html)).

Now we just run R inside the terminal by just calling \`R\`.

<br>

### Install R packages

##### 1. Install `r-base-dev`

R packages can be installed the `install.packages()` function in R, but it depends on `r-base-dev` package to compile source code for some R packages.


```r
sudo apt install r-base-dev
```

##### 2. Install system dependencies

Some R packages have system dependencies. Here we will install the system dependencies for the [`tidyverse`](https://www.tidyverse.org/) collection of packages.


```r
sudo apt install libcurl4-openssl-dev libssl-dev libxml2-dev
```

Now we can install `tidyverse` from within R by running `install.packages("tidyverse")`.

##### 3. Handling the *is not writable* error

This error is very old and still persistent (see [here](https://stackoverflow.com/questions/32540919/library-is-not-writable) and [here](https://stat.ethz.ch/pipermail/r-help/2021-April/470787.html)).

    Installing package into ???/usr/local/lib/R/site-library???
    (as ???lib??? is unspecified)
    Warning in install.packages :
      'lib = "/usr/local/lib/R/site-library"' is not writable

There are multiple ways around it (see [here](https://stat.ethz.ch/pipermail/r-help/2021-April/470792.html) for adding user to group, [here](https://stat.ethz.ch/pipermail/r-help/2021-April/470788.html) for installing as root, [here](https://stat.ethz.ch/pipermail/r-help/2021-April/470793.html) for using per-project libraries) and although it may create problems with multi-user multi-project workflows, we will just use a **personal library** (advised [here](https://stat.ethz.ch/pipermail/r-help/2021-April/470790.html)).

Just accept when R asks if you would like to create a personal library.

<br>

### Install RStudio

##### 1. Install `gdebi-core`

To install both RStudio and RStudio Server we will need `gdebi-core` to install them from `.deb` archive files.


```r
sudo apt update
# check if gdebi is already installed or not
dpkg -l | grep gdebi
# install it
sudo apt -y install gdebi-core
```

##### 2. Install RStudio

RStudio IDE official download page is [here](https://www.rstudio.com/products/rstudio/download/#download).

The installation is as straight forward as:


```r
wget https://download1.rstudio.org/desktop/bionic/amd64/rstudio-2022.02.3-492-amd64.deb
sudo gdebi rstudio-2022.02.3-492-amd64.deb
```

But I like to manually download the `.deb` files in `./Download` folder and install from there:


```r
cd ./Download
sudo gdebi $(ls | grep rstudio-2022)
```

<br>

### Install RStudio Server

RStudio Server provides a browser based interface to R running on a remote Linux server.

It is important to note from the very begining that RStudio Server by default does not allows system users (such as root) to authenticate, so we need a normal user account with sudo privilege on the server.

As mentioned above, to install both RStudio and RStudio Server we will need `gdebi-core` to install them from `.deb` archive files. Check that you have it installed.


```r
sudo apt update
# check if gdebi is already installed or not
dpkg -l | grep gdebi
```

RStudio Server official download page is [here](https://www.rstudio.com/products/rstudio/download-server/).

The installation is simple (see [official CRAN guide](https://www.rstudio.com/products/rstudio/download-server/debian-ubuntu/)):


```r
wget https://download2.rstudio.org/server/bionic/amd64/rstudio-server-2022.02.3-492-amd64.deb
sudo gdebi rstudio-server-2022.02.3-492-amd64.deb
```

By default RStudio Server runs on port 8787 and accepts connections from all remote clients. After installation you should therefore be able to navigate a web browser to the following address to access the server: `http://<server-ip>:8787`.

In the browser it will look like this:

![](Rstudioserver_capture.png)

A couple of notes related to user authentication (from [RStudio Getting Starded guide](https://support.rstudio.com/hc/en-us/articles/200552306-Getting-Started)):

-   RStudio Server will not permit logins by system users (those with user ids lower than 100).

-   User credentials are encrypted using RSA as they travel over the network.

-   You can manage users with standard Linux user administration tools like `useradd,` `userdel`, etc.

-   Each user needs to be created with a home directory.

**RStudio Server Commands**

See info on:

-   [Getting Started](https://support.rstudio.com/hc/en-us/articles/200552306-Getting-Started),

-   [Configuring the Server](https://support.rstudio.com/hc/en-us/articles/200552316-Configuring-the-Server),

-   [Managing the Server](https://support.rstudio.com/hc/en-us/articles/200532327-Configuring-the-Server),

-   [Running with a Proxy](https://support.rstudio.com/hc/en-us/articles/200552326-Running-with-a-Proxy).

Also, here we are using the free RStudio Server, but the docs on Rstudio Workbench (formally known as RStudio Server Pro - the paid product offered by RStudio) on [Administration](https://docs.rstudio.com/ide/server-pro/server_management/core_administrative_tasks.html) provide good information for both.

##### Stopping and Starting


```r
sudo rstudio-server stop
sudo rstudio-server start
sudo rstudio-server restart

sudo rstudio-server status # check the current stopped/started status of the server

sudo rstudio-server reload # reload the server???s configuration without restarting it
```

##### Managing Active Sessions


```r
sudo rstudio-server active-sessions # list all currently active sessions

sudo rstudio-server suspend-session <pid> # suspend an individual session, session data is not lost during a suspend
sudo rstudio-server force-suspend-session <pid> # a "force" variation of the suspend command which will send an interrupt to the session to request the termination of any running R command
sudo rstudio-server force-suspend-all  

sudo rstudio-server kill-session <pid>  # if unable to cooperatively suspend an R session, kill it
sudo rstudio-server kill-all   
```

##### Taking the Server Offline


```r
sudo rstudio-server offline
sudo rstudio-server online
```

##### Special tip: Rstudio Server webapp

If you prefer the RStudio Desktop look to the in-browser one when working on RStudio Server, you can create a webapp launcher for it (see [here](https://stackoverflow.com/questions/26141396/can-rstudio-desktop-be-used-as-a-client-to-rstudio-server-instead-of-the-web-i) and [here](https://askubuntu.com/questions/31427/how-do-i-put-a-web-application-on-the-launcher)).

Disclaimer: I am not sure why you would like to do this, if you are working remotely on the server and if you are not just use RStudio Desktop.

Steps:

1.  Login to the rstudio server from the browser (\<server_ip\>:8787).

2.  On the menu bar, go to File (or Tools in some cases) -\> Create application shortcut. A dialogue box will appear for the shortcut.

3.  Add it to your desktop by selecting and run it as if you're running Rstudio Desktop.

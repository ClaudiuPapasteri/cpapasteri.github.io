---
title: 'HomeLab 6: Dockerized RStudio Server, packages, persistent storage and SSL certs'
author: Claudiu C. Papasteri
date: '2022-10-16'
slug: homelab-6-dockerized-rstudio-server-packages-persistent-storage-and-ssl-certs
categories:
  - HomeLab
  - psychlab
  - R
  - Ubuntu
  - Rblog
tags:
  - HomeLab
  - psychlab
  - R
  - Rstudio
  - RStudio Server
  - packages
  - Ubuntu
subtitle: ''
summary: ''
authors: []
lastmod: '2022-10-16T16:00:53+03:00'
featured: yes
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: []
---


### Reason

R is used together with Docker for Reproducible Research and isolated development environments. Apart from the transparency, portability, extensibility and reusability that Docker provides in these context, having RStudio Server accessible in the browser from anywhere is awesome. 

My main aim for this post, and the next one about Shiny Server, is to have at my disposal two services that share some resources but remain separate for ease of maintenance. 

I decided that the Shiny Server should be its separate image, but it will need to access the same user installed package library (library folder). This is where volumes come into play. Also, for easily transferring files (like R projects and scripts) to the container, bind mounts are need.

Finally, this process is made easy by the [Rocker Project](https://rocker-project.org/) that offers [docker images for RStudio Server](https://rocker-project.org/images/versioned/rstudio.html) and a simple way of creating your own images.

For R versions >= `4.0.0` you should use the [`rocker-versioned2` images](https://github.com/rocker-org/rocker-versioned2) that are based on Ubuntu LTS.

To make matters even simpler, these images come prepackaged with important system dependencies and multiple sets of R packages. The images build upon one another by using the [`rocker_scripts`](https://github.com/rocker-org/rocker-versioned2/tree/master/scripts) that come with each image.

We will use the [`rocker/verse`](https://github.com/rocker-org/rocker-versioned2/blob/master/dockerfiles/verse_devel.Dockerfile) image. This image is large and provides many packages for convenience. For reference, here is the versioned stack from the Rocker Project for the `rocker-versioned2` images:

| image                                       | base image                                  | description                                                                    | pulls                                                                   |
|---------------------------------------------|---------------------------------------------|--------------------------------------------------------------------------------|-------------------------------------------------------------------------|
| [`rocker/r-ver`](versioned/r-ver.md)        | [`ubuntu`](https://hub.docker.com/_/ubuntu) | Install R from source and set RSPM as default CRAN mirror                      | ![Docker Pulls](https://img.shields.io/docker/pulls/rocker/r-ver)       |
| [`rocker/rstudio`](versioned/rstudio.md)    | `rocker/r-ver`                              | Adds RStudio Server                                                            | ![Docker Pulls](https://img.shields.io/docker/pulls/rocker/rstudio)     |
| [`rocker/tidyverse`](versioned/rstudio.md)  | `rocker/rstudio`                            | Adds tidyverse packages & devtools                                             | ![Docker Pulls](https://img.shields.io/docker/pulls/rocker/tidyverse)   |
| [`rocker/verse`](versioned/rstudio.md)      | `rocker/tidyverse`                          | Adds tex & publishing-related package                                          | ![Docker Pulls](https://img.shields.io/docker/pulls/rocker/verse)       |


### 1. Install Docker and Docker Compose 

I provide a walkthrough on this in my [HomeLab 3 blog post](https://claudiu.psychlab.eu/post/homelab-3-install-configure-guacamole-as-docker-container/).


### 2. Bring RStudio Server container up

Created directories for volumes/bind mounts


```r
sudo apt update
sudo apt upgrade
```


```r
cd $HOME
cd Installs/
# make folders for volumes/bind mounts
mkdir rstudio
cd rstudio/
mkdir renv 
mkdir r-projects 
mkdir r-scripts 
mkdir shiny-apps
```

My `docker-compose` was inspired by [repo of TelethonKids](https://github.com/TelethonKids/rstudio/blob/master/docker-compose.yml) and one of its [forks](https://github.com/rgarofalo/docker-compose-rstudio-server).

docker-compose.yaml:


```r
version: "3.6"

services:

  rstudio:
    image: rocker/verse:latest
    container_name: rstudio
    restart: unless-stopped
    #env_file: .rstudio.env     # can declare password in here if needed
    ports:
      - 8787:8787
    environment:
      - PASSWORD=password       # change this
    volumes:
      - type: volume
        source: rstudio
        target: /home/rstudio
        #volume:
          #no-copy: true
      - type: bind
        source: ./r-projects
        target: /home/rstudio/r-projects
      - type: bind
        source: ./r-scripts
        target: /home/rstudio/r-scripts 
      - type: bind
        source: ./renv
        target: /home/rstudio/renv
      - type: bind
        source: ./shiny-apps
        target: /home/rstudio/shiny-apps  
      - type: volume
        source: r_libs
        target: /usr/local/lib/R
        #volume:
          #no-copy: true

volumes:
  r_libs:
  rstudio:
```

Move the `docker-compose.yaml` to `rstudio` directory 


```r
# move the docker-compose.yaml to rstudio/
#  e.g. sudo mv /home/vncuser/docker-compose.yaml /home/claudiu/Installs/rstudio
```

Bring container up:


```r
sudo docker compose up -d
```

Visit local host http://127.17.0.1:8787 or local IP (in my case http://192.168.0.2:8787).
Log in with:
 - user: `rstudio`
 - password: `password` (you should change this in the `docker-compose.yaml` file)


### 3. Reverse Proxy RStudio Server

As outline in previous posts, I use Nginx Proxy Manager for reverse proxying. To add a Proxy Host, connect to the Administrator user of Nginxpm and do the following: 

Hosts > Add Proxy Host

-   Details

    -   Domain Names: rserv.psychlab.eu
    -   Scheme: http
    -   IP: 192.168.0.2
    -   Port: 8787
    -   Cache Assets: disabled
    -   Block Common Exploits: enabled
    -   Websockets Support: enabled
    -   Access List: Publicly Accessible

![]({{< blogdown/postref >}}rstudio_npm_1.png)

-   SSL

    -   SSL Certificate: *.psychlab.eu, psychlab.eu
    -   Force SSL: enabled
    -   HSTS Enabled: enabled
    -   HTTP/2 Support: enabled
    -   HSTS Subdomains: disabled

![]({{< blogdown/postref >}}rstudio_npm_2.png)

-   Custom Locations
    -   (nothing)

-   Advanced
    -   (nothing)

If all went well you can access RStudio Server by going to your subdomain (in my case its `rserv.psychlab.eu`).


### 4. Test RStudio Server with persitent storage

![]({{< blogdown/postref >}}rstudio_1.png)

Install `renv` package in R:


```r
install.packages("renv")
```

Result in R:


```r
renv::paths$library()
# [1] "/home/rstudio/renv/library/R-4.2/x86_64-pc-linux-gnu"
list.files("/home/rstudio/renv/")
# character(0)
```

In terminal:


```r
cd renv/ 
cat > test.txt
```

In R:


```r
list.files("/home/rstudio/renv/")
# [1] "test.txt"
```

In terminal:


```r
sudo rm test.txt
```

New result in R:


```r
list.files("/home/rstudio/renv/")
# character(0)
```

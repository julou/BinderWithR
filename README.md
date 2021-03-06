
This repo serves as a playground to release interactive notebooks.
To illustrate what interactive notebooks are, this repository can be loaded in an interactive RStudio session using this badge:

<!-- badges: start -->
[![Launch Rstudio Binder](http://mybinder.org/badge_logo.svg)](https://mybinder.org/v2/gh/julou/BinderWithR/master?urlpath=rstudio)
<!-- badges: end -->

## Context

The envisioned workflow is as follows:

- research work done locally (i.e. using a personal account) 
  + files contained in an R project
  + execution environment documented with `renv`
  + changes tracked with git (possibly synced with a repo on GitHub)
- at the time of release, create a binder instance for this repo
  + if needed, create a synced GitHub repository
  + create a dockerfile that can be used by Binder to launch a running instance of the rstudio project (in principle doable using `holepunch`'s `write_compendium_description()`, `write_dockerfile()`, and `build_binder()`).
  + add a binder badge to the readme file.


Several players must be involved to do this:

- RStudio as an IDE for working on the R project, using [Git](https://guides.github.com/introduction/git-handbook/) and [`renv`](https://rstudio.github.io/renv/articles/renv.html).
- GitHub to sync an online version of your R project
- [Binder](https://mybinder.org) to launch an online session of RStudio, in which your project is loaded.  
*Binder was initially developed to serve interactive Jupyter notebooks (for python code), and later extended to run R and RStudio. While Binder is the software underlying the launch of interactive online session, mybinder.org is a server managed by Binder's developers to offer Binder functionality for free (with limited resources); you can think of the relationship between Binder and mybinder.org in the same way as of the relationship between Git and GitHub.*  
Launching a Binder session implies to define the entire computing environment, which requires a few more tools.
- Docker for defining the environment in which R is run as a *container*.
- Since compiling R, its packages and its dependencies can take long, pre-maid containers are available for each R version from the [Rocker Project](http://rocker-project.org).
- [`holepunch`](https://karthik.github.io/holepunch/articles/getting_started.html) is an R package developed to help make an R project compatible with Binder.



## How it works in real life

After one day experimenting with these tools, my overall feeling is that these tools are usable but not fully mature yet.
I first created this minimalist R project with `renv` used R-4.0.1. It turned out later that rocker/binder [doesn't have an image for 4.0.1](https://github.com/rocker-org/rocker-versioned2/issues/67), and has [useless bugged images for 4.0.0 and 4.0.2](https://github.com/jupyterhub/jupyter-rsession-proxy/issues/92#issuecomment-653473022). So I branched this project to use R-3.6.3. Using `holepunch` triggered various errors including the fact that [its current version requires to downgrade `usethis` to 1.5.1](https://github.com/karthik/holepunch/issues/50#issuecomment-643446708) (from current 1.6.1)...

The other big challenge is to achieve reproducibility of the R environment. At the end of the day, it turns out that setting up Rocker manually seems to be more efficient in my case.

### Using Rocker manually

The alternative is to launch a bare Rocker instance and to install the relevant package version using `renv::restore()`.
To minimise the time of package installation, it is possible to use binaries pre-compiled for Linux from RStudio's package manager (NB: the relevant system prerequisite for Rocker's images are Ubuntu 18).

Follow the instructions at https://hub.docker.com/r/rocker/binder: copy the Dockerfile example at `.binder/Dockerfile` and customise as necessary. In particular, 

- make sure to specify the rocker/binder version corresponding to the R version in your `renv.lock` file.
- append commands to fetch the GitHub repo and install the packages indicated by `renv`:  
```
# binary packages, repo version 2020-07-02
RUN R -e "renv::restore(repos = c(CRAN='https://packagemanager.rstudio.com/all/__linux__/bionic/298'))"
```

### Using `holepunch`

For the sake of completeness, I explain here why `holepunch` gave me a hard time and doesn't seem well suited in my case. This package is developed with the idea of environment snapshot in mind, i.e. a date at which the latest version is installed for R and all its packages. In practice, this doesn't match my experience since I tend to upgrade packages along the way (a given project typically takes several months / years to complete in my case). So the approach of `renv` is much more appealing. 
Practically, even using `holepunch` as a starting point on top of which to install packages with `renv` doesn't prove convenient since the Dockerfile created by `holepunch` hardcodes a certain date for the list of R package so that e.g. the needed version of `renv` (launched automatically when the project is loaded) might not be available; all this can be fixed manually but is admittedly confusing at first.

A typical `holepunch` workflow would be something like:

```{r eval=FALSE}
library(holepunch)
write_compendium_description(
  package = "Binder with R", 
  description = "A dry run with Binder, renv, holepunch and friends",
  )

# base::strsplit(R.Version()$version.string, " ")[[1]][3] %>% 
# paste0("rocker/binder:", .) %>% 
# write_dockerfile(maintainer = "Thomas Julou")
# ignore warnings

write_dockerfile(maintainer = "Thomas Julou")

generate_badge()

build_binder()

```

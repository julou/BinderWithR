## Use a tag instead of "latest" for reproducibility
FROM rocker/binder:4.0.1
LABEL maintainer='Thomas Julou'

## Declares build arguments
ARG NB_USER
ARG NB_UID

## Copies your repo files into the Docker Container
USER root
COPY . ${HOME}
## Enable this to copy files from the binder subdirectory
## to the home, overriding any existing files.
## Useful to create a setup on binder that is different from a
## clone of your repository
## COPY binder ${HOME}
RUN chown -R ${NB_USER} ${HOME}

## Become normal user again
USER ${NB_USER}

## Run an install.R script, if it exists.
#RUN if [ -f install.R ]; then R --quiet -f install.R; fi

## from https://rstudio.github.io/renv/articles/docker.html
# WORKDIR /project
# COPY renv.lock renv.lock
# RUN R -e "message('wd: ', getwd())"
# RUN R -e "renv::restore()"
# RUN R -e "renv::restore(repos = getOption('repos')['CRAN'])" # by default Binder uses rstudio's packagemanager for R≥4.0
# RUN R -e "renv::restore(repos = c(CRAN='https://packagemanager.rstudio.com/all/__linux__/focal/298'))" # binary packages, repo version 2020-07-02

RUN rm DESCRIPTION.1; exit 0
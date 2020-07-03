FROM rocker/binder:3.6.3
LABEL maintainer='Thomas Julou'
USER root
COPY . ${HOME}
RUN chown -R ${NB_USER} ${HOME}
USER ${NB_USER}

RUN wget https://github.com/julou/BinderWithR/raw/R-3.6/DESCRIPTION
RUN R --no-init-file -e "options(repos = list(CRAN = 'https://packagemanager.rstudio.com/all/__linux__/bionic/298'))"
RUN R -e "renv::restore()"
# RUN wget https://github.com/julou/BinderWithR/raw/R-3.6/DESCRIPTION && R -e "options(repos = list(CRAN = 'http://mran.revolutionanalytics.com/snapshot/2020-07-03/')); devtools::install_deps()"

RUN rm -rf kitematic
RUN rm DESCRIPTION.1; exit 0

FROM rocker/binder:4.0.2
LABEL maintainer='Thomas Julou'
ARG NB_USER
ARG NB_UID
USER root
COPY . ${HOME}
RUN chown -R ${NB_USER} ${HOME}
USER ${NB_USER}



RUN wget https://github.com/julou/BinderWithR/raw/master/DESCRIPTION && R -e "options(repos = list(CRAN = 'http://mran.revolutionanalytics.com/snapshot/2020-07-03/')); devtools::install_deps()"

RUN rm DESCRIPTION.1; exit 0

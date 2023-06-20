# Dockerfile

# Specify a Cloudera Data Science Workbench base image
FROM docker.repository.cloudera.com/cdsw/engine:8
RUN rm /etc/apt/sources.list.d/*

# install java develop kit and rJava
RUN apt-get update && \
    apt-get install -y default-jdk
RUN R CMD javareconf
RUN R -e "install.packages('rJava', dependencies = TRUE)"

# install devtools
RUN apt-get update && \
    apt-get install -y \
    build-essential \ 
    libcurl4-gnutls-dev \
    libxml2-dev \
    libssl-dev \
    libgit2-dev \
# install other
    libfontconfig1-dev \
    libcairo2-dev \
    libharfbuzz-dev \
    libfribidi-dev \
    libfreetype6-dev \
    libpng-dev \
    libtiff5-dev \
    libjpeg-dev \
    libboost-dev \
    librsvg2-dev \
    libmagick++-dev

RUN apt install -y software-properties-common
RUN apt-add-repository ppa:cran/libgit2
RUN apt-get update && apt-get install -y libssh2-1-dev libgit2-dev alien
ADD pkgs /home/cdsw/pkgs

# ODBC oracle client install
RUN alien -i /home/cdsw/pkgs/oracle-instantclient12.1-basic-12.1.0.2.0-1.x86_64.rpm
RUN sh /home/cdsw/pkgs/instantclient_12_1/odbc_update_ini.sh / /usr/lib/oracle/12.1/client64/lib oracle-12
RUN echo /usr/lib/oracle/12.1/client64/lib/ > /etc/ld.so.conf.d/oracle.conf
RUN cp /home/cdsw/pkgs/instantclient_12_1/libsqora.so.12.1 /usr/lib/oracle/12.1/client64/lib
RUN alien -i /home/cdsw/pkgs/oracle-instantclient12.1-odbc-12.1.0.2.0-1.x86_64.rpm

RUN R -e "remotes::install_version('fastmap', version = '1.1.0', upgrade='never'); remotes::install_version('rlang', version = '0.3.0.1', upgrade = 'never'); remotes::install_version('cli', version = '3.3.0', upgrade = 'never');  remotes::install_version('xfun', version='0.4', upgrade = 'never'); remotes::install_version('memoise', version = '1.1.0', upgrade='never'); remotes::install_version('gdtools', version='0.1.7', upgrade='never'); remotes::install_version('cachem', version='1.0.7', upgrade='never'); remotes::install_version('devtools', version='2.0.1', upgrade='never'); remotes::install_version('tzdb', version='0.2.0', upgrade='never');remotes::install_version('vroom', version='1.6.1', upgrade='never'); remotes::install_version('duckdb', version = '0.2.3', upgrade='never'); install.packages('BH', upgrade='never'); baseR = as.data.frame(installed.packages()); pkgs = read.csv('/home/cdsw/pkgs/pkgs.csv'); toInstall = setdiff(pkgs[,1], baseR[,1]); installList = subset(pkgs, Package %in% toInstall); try({for(i in 1:nrow(installList)){remotes::install_version(installList[i,1], version=installList[i,3][1], dependency = T, upgrade = 'never')}}); remotes::install_version('ggiraph', version='0.6.0', upgrade = 'never'); remotes::install_version('latticeExtra', version='0.6-28', upgrade = 'never'); remotes::install_version('Hmisc', version='4.1-1', upgrade = 'never'); remotes::install_version('MatrixModels', version='0.4-1', upgrade='never'); remotes::install_version('mvtnorm', version='1.0-8', upgrade='never'); remotes::install_version('multcomp', version='1.4-8', upgrade = 'never'); remotes::install_version('openxlsx', version='4.1.0', upgrade='never'); remotes::install_version('RcppEigen', version='0.3.3.5.0', upgrade='never'); remotes::install_version('reticulate', version='1.1', upgrade='never'); remotes::install_version('rjson', version='0.2.20', upgrade='never'); remotes::install_version('rms', version='5.1-2', upgrade = 'never'); remotes::install_version('blob', version='1.2.3', upgrade = 'never'); remotes::install_version('Rcpp', version='1.0.9', upgrade = 'never'); remotes::install_version('RSQLite', version='2.2.15', upgrade = 'never'); remotes::install_version('officer', version='0.3.2', upgrade = 'never'); remotes::install_version('rvg', version='0.1.9', upgrade = 'never'); remotes::install_version('shiny', version='1.2.0', upgrade = 'never'); remotes::install_version('shinycssloaders', version='0.2.0', upgrade = 'never'); remotes::install_version('shinydashboard', version='0.7.1', upgrade = 'never'); remotes::install_version('triebeard', version='0.3.0', upgrade = 'never'); remotes::install_version('urltools', version='1.7.3', upgrade = 'never'); remotes::install_version('odbc', version='1.3.3', upgrade = 'never'); install.packages('log4r', upgrade = 'never'); install.packages('RPostgres', upgrade = 'never');"

# create folders
RUN mkdir /home/cdsw/temp /home/cdsw/jdbc /home/cdsw/study /home/cdsw/result
RUN R -e "setwd('/home/cdsw/pkgs'); remotes::install_version('Andromeda', version='0.6.1', upgrade = 'never'); remotes::install_version('readr', version='1.3.0', upgrade = 'never'); remotes::install_version('SqlRender', version='1.9.2', upgrade = 'never'); remotes::install_version('DatabaseConnector', version='5.0.4', upgrade = 'never'); remotes::install_version('ParallelLogger', version='2.0.2', upgrade = 'never'); remotes::install_github('OHDSI/FeatureExtraction', ref='v3.1.1', upgrade = 'never'); remotes::install_version('Cyclops', version='3.1.2', upgrade = 'never'); remotes::install_version('EmpiricalCalibration', version='2.1.0', upgrade = 'never'); remotes::install_github('OHDSI/ROhdsiWebApi', ref='v1.3.1', upgrade = 'never'); remotes::install_github('OHDSI/CohortMethod', ref='v4.2.0', upgrade = 'never'); remotes::install_version('Eunomia', version='1.0.1', upgrade = 'never'); remotes::install_github('OHDSI/CohortDiagnostics', ref='v2.0.0', upgrade = 'never');"

# install Oracle JDBC driver
RUN R -e "DatabaseConnector::downloadJdbcDrivers(dbms = 'oracle', pathToDriver = '/home/cdsw/jdbc')"

# install CDMConnector
RUN R -e "zipfile='/home/cdsw/pkgs/CDMConnector-main.zip'; exdir = '/home/cdsw/pkgs'; unzip(zipfile = zipfile, exdir = exdir); setwd(file.path(exdir, 'CDMConnector-main')); devtools::install(upgrade='never'); install.packages('PatientProfiles', upgrade = 'never'); remotes::install_github('oxford-pharmacoepi/poLCA_oxf', upgrade = 'never')"
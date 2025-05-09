# Rocker verse with conda notebook environment; 
# rstudio user with /home/jovyan home dir
FROM ghcr.io/nmfs-opensci/container-images/py-rocket-base:4.4-3.10
LABEL org.opencontainers.image.maintainers="eli.holmes@noaa.gov"
LABEL org.opencontainers.image.author="eli.holmes@noaa.gov"
LABEL org.opencontainers.image.source=https://github.com/nmfs-opensci/container-images/py-rocket-geospatial
LABEL org.opencontainers.image.description="Geospatial Python (3.10) and R (4.4) image"
LABEL org.opencontainers.image.licenses=Apache2.0
LABEL org.opencontainers.image.version=2024.07.11

USER root

# install the geospatial libraries and R spatial
RUN /rocker_scripts/install_geospatial.sh

# Install extra tex packages that are not installed by default
RUN if command -v tlmgr > /dev/null; then \
      echo "Installing texlive collection-latexrecommended..."; \
      tlmgr install collection-latexrecommended; \
      tlmgr install pdfcol tcolorbox eurosym upquote adjustbox titling enumitem ulem soul rsfs; \
    fi

USER ${NB_USER} 

# install a few more R geospatial libraries mainly for CoastWatch
COPY install.R install.R
RUN Rscript install.R && rm install.R

# install the Python geospatial libraries
COPY environment.yml environment.yml
RUN conda env update -n notebook -f environment.yml \
    && conda clean --all \
    && rm environment.yml

COPY requirements.txt requirements.txt
RUN ${NB_PYTHON_PREFIX}/bin/pip install --no-cache -r requirements.txt \
    && rm requirements.txt
    
# set the proj lib
ENV PROJ_LIB=/srv/conda/envs/notebook/share/proj

# Set user dir
WORKDIR /home/jovyan

FROM quay.io/jupyter/base-notebook:python-3.11

RUN apt-get update &&            \
    apt-get install -y --no-install-recommends \
        build-essential\
        git \
        python3-dev &&\
    apt-get clean && \
    apt-get autoremove && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*; \
    pip install --upgrade pip --ignore-installed certifi;  \
    pip install jupyter-book==0.13.0  \
                repo2data==2.9.1  \
                scipy \
                plotly \
                flask \
                ipywidgets \
                nbconvert \
                jupyterlab  \
                jupytext\
                matplotlib \
                chart_studio

COPY ../ ${HOME}
USER root
RUN chown -R ${NB_UID} ${HOME}
USER ${NB_USER}

ARG NB_USER=jovyan
ARG NB_UID=1000
ENV USER ${NB_USER}
ENV NB_UID ${NB_UID}
ENV HOME /home/${NB_USER}

RUN adduser --disabled-password \
    --gecos "Default user" \
    --uid ${NB_UID} \
    ${NB_USER}

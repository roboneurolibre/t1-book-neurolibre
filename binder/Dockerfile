FROM python:3.8-slim

user root
# install the notebook package
RUN pip install --no-cache --upgrade pip; \
    pip install jupyter-book \
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
FROM alpine:latest
MAINTAINER Wise.io, Inc. <contact@wise.io>

# Inspired from :
# * https://github.com/jupyter/docker-stacks
# * https://github.com/CognitiveScale/alpine-miniconda
# * https://github.com/show0k/poppy-docker/blob/master/alpine-minimal-notebook/Dockerfile

# Install glibc and usefull packages

RUN echo "@testing http://nl.alpinelinux.org/alpine/edge/testing" >> /etc/apk/repositories
RUN apk update && apk upgrade 
RUN apk --update add  bash git curl ca-certificates bzip2 unzip sudo libstdc++ glib libxext libxrender wget postgresql-dev

RUN wget --quiet https://github.com/krallin/tini/releases/download/v0.9.0/tini && \
    echo "faafbfb5b079303691a939a747d7f60591f2143164093727e870b289a44d9872 *tini" | sha256sum -c - && \
    mv tini /usr/local/bin/tini && \
    chmod +x /usr/local/bin/tini

RUN curl "https://circle-artifacts.com/gh/andyshinn/alpine-pkg-glibc/6/artifacts/0/home/ubuntu/alpine-pkg-glibc/packages/x86_64/glibc-2.21-r2.apk" -o glibc.apk 
RUN apk add --allow-untrusted glibc.apk 
RUN curl "https://circle-artifacts.com/gh/andyshinn/alpine-pkg-glibc/6/artifacts/0/home/ubuntu/alpine-pkg-glibc/packages/x86_64/glibc-bin-2.21-r2.apk" -o glibc-bin.apk 
RUN apk add --allow-untrusted glibc-bin.apk 
RUN /usr/glibc/usr/bin/ldconfig /lib /usr/glibc/usr/lib 
RUN rm -rf glibc*apk /var/cache/apk/*


# Configure environment
ENV CONDA_DIR /opt/conda
ENV PATH $CONDA_DIR/bin:$PATH
ENV SHELL /bin/bash
ENV NB_USER jovyan
ENV NB_UID 1000
ENV LC_ALL en_US.UTF-8
ENV LANG en_US.UTF-8
ENV LANGUAGE en_US.UTF-8

# Create jovyan user with UID=1000 and in the 'users' group
RUN adduser -s /bin/bash -u $NB_UID -D $NB_USER && \
    mkdir -p /opt/conda && \
    chown jovyan /opt/conda

USER jovyan

# Setup jovyan home directory
RUN mkdir /home/$NB_USER/work && \
    mkdir /home/$NB_USER/.jupyter && \
    mkdir /home/$NB_USER/.local

# Install conda as jovyan
RUN cd /tmp && \
    mkdir -p $CONDA_DIR && \
    curl https://repo.continuum.io/miniconda/Miniconda-latest-Linux-x86_64.sh  -o miniconda.sh && \
    /bin/bash miniconda.sh -f -b -p $CONDA_DIR && \
    rm miniconda.sh

ONBUILD ENV PATH $CONDA_DIR/bin:$PATH

# Install some essential data science packages in python, including psycopg2
RUN conda install scipy numpy scikit-learn nose readline pandas matplotlib seaborn dateutil ipython-notebook nltk pip
RUN conda install psycopg2
RUN conda install cython hdf5 pytables
RUN conda install ipywidgets
RUN conda clean -i -l -t -y

# Get plotly
RUN pip install plotly

# Get the latest gensim
RUN pip install gensim

# get all the nltk data
# RUN python -m nltk.downloader all

USER root

ENV PEM_FILE /tmp/key.pem
# $PASSWORD will get `unset` within notebook.sh, turned into an IPython style hash
ENV PASSWORD Dont make this your default
ENV USE_HTTP 1
EXPOSE 8888

# Add current files to / and set entry point.

ADD . /workspace
WORKDIR /workspace
RUN chown $NB_USER /workspace
RUN chmod a+rw /workspace
ADD notebook.sh /notebook.sh
RUN chmod a+x /notebook.sh

ENTRYPOINT ["tini", "--"]

# Switch back to jovyan to avoid accidental container runs as root
USER jovyan
CMD ["/notebook.sh"]



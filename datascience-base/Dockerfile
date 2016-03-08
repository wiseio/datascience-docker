FROM ubuntu:latest

MAINTAINER Wise.io, Inc. <help@wise.io>

ENV DEBIAN_FRONTEND noninteractive

# Install packages ... change the timezone line if you're not in Pacific time
RUN apt-get -y update && apt-get install -y wget nano locales curl unzip wget openssl libhdf5-dev libpq-dev \
    && apt-get clean && dpkg-reconfigure locales && locale-gen en_US.UTF-8 \
    && echo "America/Los_Angeles" > /etc/timezone && dpkg-reconfigure --frontend noninteractive tzdata \
    && apt-get autoremove \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Install and setup minimal Anaconda Python distribution, then tear down temp files
RUN wget http://repo.continuum.io/miniconda/Miniconda-latest-Linux-x86_64.sh -O miniconda.sh \
    && bash miniconda.sh -b -p /anaconda \
    && conda install scipy numpy scikit-learn nose readline pandas matplotlib seaborn dateutil ipython-notebook nltk \
       pip psycopg2 cython hdf5 pytables ipywidgets \
    && conda clean -i -l -t -y \
    && rm miniconda.sh

# Get the pip packages
ADD requirements.txt /
RUN tempdir=$(mktemp -d /tmp/wheelhouse-XXXXX) \
    && pip install --download=$tempdir -r /requirements.txt \
    && rm -fr $tempdir

ENV LANGUAGE en_US.UTF-8
ENV LANG en_US.UTF-8
ENV PATH /anaconda/bin:$PATH
# For image inheritance.
ONBUILD ENV PATH /anaconda/bin:$PATH

ENV PEM_FILE /key.pem
# $PASSWORD will get `unset` within notebook.sh, turned into an IPython style hash
ENV PASSWORD Dont make this your default
ENV USE_HTTP 1

# Add current files to / and set entry point.
ADD . /workspace
WORKDIR /workspace
ADD notebook.sh /notebook.sh
RUN chmod a+x /notebook.sh

EXPOSE 8888

CMD ["/notebook.sh"]


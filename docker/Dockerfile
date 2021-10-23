FROM ubuntu:18.04
#FROM python:3.10

# All this to try to get ppa:ubuntugis/ppa (GIS)
RUN apt update && apt dist-upgrade -y
RUN apt install software-properties-common -y
RUN add-apt-repository ppa:ubuntugis/ppa && apt-get update
RUN apt update

# Get Python & Conda
RUN apt-get -qq update \
    && apt-get -qq upgrade \
    && apt-get -qq -y install curl bzip2 \
    && curl -sSL https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh -o /tmp/miniconda.sh \
    && bash /tmp/miniconda.sh -bfp /usr/local \
    && rm -rf /tmp/miniconda.sh \
    && conda install -y python=3 \
    && conda update conda \
    && apt-get install default-jre -y \
	#&& conda install -c conda-forge pyspark=${SPARK_VERSION} \ No need of Spark/PySpark (maybe)
    && apt-get -qq -y remove curl bzip2 \
    && apt-get -qq -y autoremove \
    && apt-get autoclean \
    && rm -rf /var/lib/apt/lists/* /var/log/dpkg.log \
    && conda clean --all --yes

ENV PATH /opt/conda/bin:$PATH

# Installing key dependencies
RUN apt-get update && apt-get --yes install \
    python3-dev python3-pip python3-wheel python3-setuptools

RUN apt-get update && apt-get --yes install \
    graphviz git openssh-client libpq-dev

RUN apt-get update && apt-get --yes install \
    # Suggestions from https://mothergeo-py.readthedocs.io/en/latest/development/how-to/gdal-ubuntu-pkg.html
    gdal-bin libgdal-dev locales g++ gcc

RUN apt-get update && apt-get --yes install \
    python-gdal python3-gdal

# # Ensure locales configured correctly
# RUN locale-gen en_US.UTF-8
# ENV LC_ALL='en_US.utf8'
#
# ENV TZ 'GB'
# RUN echo $TZ > /etc/timezone && \
#     apt-get install -y tzdata && \
#     rm /etc/localtime && \
#     ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && \
#     dpkg-reconfigure -f noninteractive tzdata && \
#     apt-get clean

# Add unstable repo to allow us to access latest GDAL builds
#RUN echo deb http://ftp.uk.debian.org/debian unstable main contrib non-free >> /etc/apt/sources.list
#RUN apt-get update

# Existing binutils causes a dependency conflict, correct version will be installed when GDAL gets intalled
RUN apt-get remove -y binutils

# Set python aliases for python3
#RUN echo 'alias python=python3' >> ~/.bashrc
#RUN echo 'alias pip=pip3' >> ~/.bashrc

ARG CPLUS_INCLUDE_PATH=/usr/include/gdal
ARG C_INCLUDE_PATH=/usr/include/gdal

RUN pip install numpy
RUN pip install psycopg2-binary
RUN pip install GDAL

#RUN apt-get update && apt-get --yes install \
    #build-essential cmake \
    #postgresql-contrib \
    #libssl-dev libffi-dev gcc \
    #libxml2-dev libxslt1-dev zlib1g-dev g++ \

# ############################################### Get Jupyter Lab
# Config for Jupyter Easy Start
RUN pip install jupyter jupyterlab \
    && jupyter-notebook --generate-config \
    && ipython profile create \
    && echo "c.NotebookApp.token = ''" >> ~/.jupyter/jupyter_notebook_config.py \
    && echo "c.NotebookApp.ip = '0.0.0.0'" >> ~/.jupyter/jupyter_notebook_config.py \
    && echo "c.NotebookApp.allow_root = True" >> ~/.jupyter/jupyter_notebook_config.py \
    && apt-get -qq -y autoremove \
    && apt-get autoclean

# ############################################### Get Last steps
COPY init_notebook.py /root/.ipython/profile_default/startup

# Install requirements file
COPY requirements.txt requirements.txt
RUN pip install -r requirements.txt

EXPOSE 8888

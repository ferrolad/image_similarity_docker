# start with a base image
FROM ubuntu:16.04
MAINTAINER Real Python <info@realpython.com>
ENV PYTHONUNBUFFERED 1
# install dependencies
RUN apt-get update
RUN apt-get install -y nginx supervisor
RUN apt-get install -y python python-dev python-pip python-virtualenv
#SIFT don't support opencv2
#RUN apt-get install -y python-opencv
RUN apt-get install -y python-matplotlib
RUN apt-get install -y python-scipy


#To install anaconda in order to support opencv3

RUN apt-get update --fix-missing && apt-get install -y wget bzip2 ca-certificates \
    libglib2.0-0 libxext6 libsm6 libxrender1 \
    git mercurial subversion

RUN echo 'export PATH=/opt/conda/bin:$PATH' > /etc/profile.d/conda.sh && \
    wget --quiet https://repo.continuum.io/archive/Anaconda2-4.2.0-Linux-x86_64.sh -O ~/anaconda.sh && \
    /bin/bash ~/anaconda.sh -b -p /opt/conda && \
    rm ~/anaconda.sh

RUN apt-get install -y curl grep sed dpkg && \
    TINI_VERSION=`curl https://github.com/krallin/tini/releases/latest | grep -o "/v.*\"" | sed 's:^..\(.*\).$:\1:'` && \
    curl -L "https://github.com/krallin/tini/releases/download/v${TINI_VERSION}/tini_${TINI_VERSION}.deb" > tini.deb && \
    dpkg -i tini.deb && \
    rm tini.deb && \
    apt-get clean

ENV PATH /opt/conda/bin:$PATH

ENTRYPOINT [ "/usr/bin/tini", "--" ]
CMD [ "/bin/bash" ]


RUN conda install -c https://conda.anaconda.org/menpo opencv3
RUN pip install uwsgi
RUN pip install Flask
RUN pip install pyssim
RUN apt-get install -y python-skimage
RUN conda install pymongo
#RUN export TF_BINARY_URL=https://storage.googleapis.com/tensorflow/linux/cpu/tensorflow-0.11.0-cp27-none-linux_x86_64.whl
#RUN pip install --upgrade $TF_BINARY_URL
RUN pip --no-cache-dir install \
    https://storage.googleapis.com/tensorflow/linux/cpu/tensorflow-0.11.0-cp27-none-linux_x86_64.whl
RUN pip install keras

# update working directories
ADD ./app /app
ADD ./config /config

# setup config
RUN echo "\ndaemon off;" >> /etc/nginx/nginx.conf
RUN rm /etc/nginx/sites-enabled/default

RUN ln -s /config/nginx.conf /etc/nginx/sites-enabled/
RUN ln -s /config/supervisor.conf /etc/supervisor/conf.d/

EXPOSE 80
CMD ["python", "app/app.py"]

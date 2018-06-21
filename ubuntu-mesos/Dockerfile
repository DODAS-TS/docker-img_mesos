FROM ubuntu:16.04

ENV MESOS_VERSION_TAG="1.6.0"

WORKDIR /tmp

RUN apt-get update \
    && apt-get upgrade -y --no-install-recommends \
    && apt-get install -y --no-install-recommends autoconf \ 
        automake \
        build-essential \ 
        git \ 
        iputils-ping \ 
        libapr1-dev \ 
        libcurl4-nss-dev \ 
        libsasl2-dev \ 
        libsasl2-modules \ 
        libsvn-dev \ 
        libtool \ 
        maven \ 
        openjdk-8-jdk \ 
        python-dev \ 
        python-six \ 
        python-virtualenv \ 
        tar \ 
        wget \ 
        zlib1g-dev \ 
    && apt-get autoremove \
    && apt-get clean \
    && git clone https://git-wip-us.apache.org/repos/asf/mesos.git

WORKDIR /tmp/mesos

# Select version
RUN git checkout -b "$MESOS_VERSION_TAG"

# Bootstrap (Only required if building from git repository).
RUN ./bootstrap

# Configure and build.
RUN mkdir build \
    && cd build \
    && ../configure \
    && make
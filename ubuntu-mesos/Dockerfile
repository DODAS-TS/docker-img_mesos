##
# Builder Stage
FROM ubuntu:16.04 as builder

ENV MESOS_VERSION_TAG="1.6.0"

RUN apt-get update \
    && apt-get upgrade -y --no-install-recommends \
    && apt-get install -y --no-install-recommends autoconf \ 
        automake \
        build-essential \ 
        cmake \
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
    && apt-get clean

WORKDIR /tmp

##
# Upgrade CMAKE
RUN wget https://cmake.org/files/v3.12/cmake-3.12.0-rc1.tar.gz -O cmake.tar.gz \
    && mkdir cmake \
    && tar xf cmake.tar.gz -C cmake --strip-components 1

WORKDIR /tmp/cmake
RUN cmake . \
    && make \
    && make install

##
# Get and compile mesos
WORKDIR /tmp
RUN git clone "https://git-wip-us.apache.org/repos/asf/mesos.git"
WORKDIR /tmp/mesos

# Select version
RUN git checkout "$MESOS_VERSION_TAG"

# Bootstrap (Only required if building from git repository).
RUN ./bootstrap

RUN cmake . -DCPACK_BINARY_DEB=ON -DCPACK_PACKAGE_CONTACT="mirco.tracolli@pg.infn.it" \
    && make package \
    && mv "/tmp/mesos-$MESOS_VERSION_TAG.deb" /tmp/mesos.deb

##
# Final stage
FROM ubuntu:16.04

COPY --from=builder /tmp/mesos.deb /tmp
RUN apt-get update \
    && apt-get upgrade -y --no-install-recommends \
    && dpkg -i /tmp/mesos.deb \
    && rm /tmp/mesos.deb

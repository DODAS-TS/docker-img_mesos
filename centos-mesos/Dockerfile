##
# Builder Stage
FROM centos:7 as builder

ENV MESOS_VERSION_TAG="1.6.0"

RUN yum --setopt=tsflags=nodocs -y update \
    && yum --setopt=tsflags=nodocs -y upgrade \
    && yum --setopt=tsflags=nodocs -y install epel-release \
        git \
        tar \ 
        wget \
    && wget http://repos.fedorapeople.org/repos/dchen/apache-maven/epel-apache-maven.repo -O /etc/yum.repos.d/epel-apache-maven.repo \
    && yum --setopt=tsflags=nodocs -y update \
    && yum groupinstall --setopt=tsflags=nodocs -y 'Development Tools' \
    && yum --setopt=tsflags=nodocs -y install apache-maven \
        apr-devel \
        apr-util-devel \
        cyrus-sasl-devel \
        cyrus-sasl-md5 \
        java-1.8.0-openjdk-devel \
        libcurl-devel \
        openssl-devel \
        python-devel \
        python-six \
        python-virtualenv \
        subversion-devel \
        zlib-devel \
    && yum clean all

WORKDIR /tmp

##
# Upgrade CMAKE
RUN wget https://cmake.org/files/LatestRelease/cmake-3.11.0.tar.gz -O cmake.tar.gz \
    && mkdir cmake \
    && tar xf cmake.tar.gz -C cmake --strip-components 1

WORKDIR /tmp/cmake
RUN ./bootstrap \
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

RUN cmake . -DCPACK_BINARY_RPM=ON -DCPACK_PACKAGE_CONTACT="mirco.tracolli@pg.infn.it" \
    && make package
RUN mv "mesos-$MESOS_VERSION_TAG.rpm" mesos.rpm

##
# Final stage
FROM centos:7

COPY --from=builder /tmp/mesos/mesos.rpm /tmp
RUN yum --setopt=tsflags=nodocs -y update \
    && yum --setopt=tsflags=nodocs -y upgrade \
    && rpm -i /tmp/mesos.rpm \
    && rm /tmp/mesos.rpm

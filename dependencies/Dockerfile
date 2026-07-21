ARG IMG=noble

FROM ubuntu:$IMG

ARG VERSION=9.7.1-1~ubuntu24.04.1~ppa1
ARG REPO=ppa:ginggs/deal.ii-9.7.1-backports

USER root
ENV DEBIAN_FRONTEND=noninteractive 

# Symengine
RUN cat /usr/local/lib/cmake/symengine/SymEngineConfig.cmake

RUN cd /usr/src && \
    wget https://github.com/symengine/symengine/releases/download/v0.14.0/symengine-0.14.0.tar.gz && \
    tar xvfz symengine-0.14.0.tar.gz && rm symengine-0.14.0.tar.gz &&\
    cd symengine-0.14.0 && mkdir build && cd build && \
    cmake \
    -DBUILD_BENCHMARKS=off \
    -DBUILD_SHARED_LIBS=on \
    -DBUILD_TESTS=off \
    -DINTEGER_CLASS=flint \
    -DWITH_BOOST=on  \
    -DWITH_MPC=on \
    -DWITH_MPFR=on \
    -DWITH_SYMENGINE_THREAD_SAFE=on \
    -GNinja .. && \
    ninja && ninja install && \
    cd .. && rm -rf build

RUN cat /usr/local/lib/cmake/symengine/SymEngineConfig.cmake

RUN locale-gen en_US.UTF-8
ENV LANGUAGE=en_US:en
ENV LC_ALL=en_US.UTF-8
ENV LANG=en_US.UTF-8

RUN ldconfig
ENV PYTHONPATH="/usr/local/lib/python3.8/site-packages/"

# add and enable the default user
ARG USER=dealii
ARG UID=1001
RUN adduser --disabled-password --gecos '' --uid $UID $USER
RUN adduser $USER sudo; echo '%sudo ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers

# make sure everything is in place
RUN chown -R $USER:$USER /home/$USER
USER $USER
ENV HOME=/home/$USER
ENV USER=$USER
ENV OMPI_MCA_btl="^vader"
WORKDIR $HOME

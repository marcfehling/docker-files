ARG IMG=noble

FROM ubuntu:$IMG

ARG VERSION=9.7.1-1~ubuntu24.04.1~ppa1
ARG REPO=ppa:ginggs/deal.ii-9.7.1-backports

USER root
ENV DEBIAN_FRONTEND=noninteractive 

RUN apt-get update && apt-get install -y software-properties-common \
    && add-apt-repository $REPO \
    && apt-get update && \
    apt-get install -y \
    git \
    libboost-dev \
    libboost-python-dev \
    libcgal-dev \
    libdeal.ii-dev=$VERSION \
    libflint-dev \
    libgmp-dev \
    libgtest-dev \
    libmpc-dev \
    locales \
    ninja-build \
    numdiff \
    python-is-python3 \
    python3-dev \
    python3-matplotlib \
    python3-pybind11 \
    python3-scipy\
    ssh \
    sudo \
    wget \
    && apt-get remove -y libdeal.ii-dev=$VERSION libcgal-dev \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* \
    && localedef -i en_US -c -f UTF-8 -A /usr/share/locale/locale.alias en_US.UTF-8

ENV LANGUAGE=en_US:en  
ENV LC_ALL=en_US.UTF-8

# mold
# We require at least mold 2.32 to be able to link deal.II on ARM architectures.
# See also: https://github.com/dealii/dealii/pull/18172#issuecomment-2696956258
RUN cd /usr/src && \
    wget https://github.com/rui314/mold/archive/refs/tags/v2.36.0.tar.gz && \
    tar xvfz v2.36.0.tar.gz && rm v2.36.0.tar.gz && \
    cd mold-2.36.0 && \
    ./install-build-deps.sh && \
    mkdir build && cd build && \
    cmake \
    -DCMAKE_BUILD_TYPE=Release \
    -DCMAKE_CXX_COMPILER=c++ \
    -GNinja .. && \
    ninja && ninja install && \
    cd .. && rm -rf build

# t8code
RUN cd /usr/src && \
    wget https://github.com/DLR-AMR/t8code/releases/download/v4.0.0/T8CODE-4.0.0-Source.tar.gz && \
    tar xvfz T8CODE-4.0.0-Source.tar.gz && rm T8CODE-4.0.0-Source.tar.gz && \
    cd T8CODE-4.0.0-Source && mkdir build && cd build && \
    cmake \
    -DCMAKE_BUILD_TYPE=Release \
    -DT8CODE_BUILD_AS_SHARED_LIBRARY=ON \
    -DT8CODE_BUILD_BENCHMARKS=OFF \
    -DT8CODE_BUILD_TESTS=OFF \
    -DT8CODE_BUILD_TUTORIALS=OFF \
    -DT8CODE_BUILD_EXAMPLES=OFF \
    -DT8CODE_ENABLE_MPI=ON \
    -DT8CODE_ENABLE_VTK=OFF \
    -DT8CODE_ENABLE_OCC=OFF \
    -DT8CODE_USE_SYSTEM_SC=ON \
    -DT8CODE_USE_SYSTEM_P4EST=ON \
    -GNinja .. && \
    ninja && ninja install && \
    cd .. && rm -rf build

RUN locale-gen en_US.UTF-8  
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

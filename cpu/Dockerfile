FROM ubuntu:16.04
MAINTAINER caffe-maint@googlegroups.com

RUN apt-get update && apt-get install -y --no-install-recommends \
        autoconf \
        automake \
        libtool \
        curl \
        unzip \
        build-essential \
        cmake \
        git \
        wget \
        libatlas-base-dev \
        libboost-all-dev \
        libgflags-dev \
        libgoogle-glog-dev \
        libhdf5-serial-dev \
        libleveldb-dev \
        liblmdb-dev \
        libopencv-dev \
        libprotobuf-dev \
        libsnappy-dev \
        protobuf-compiler \
        python3-dev \
        python3-numpy \
        python3-pip \
        python-scipy \
        python-dateutil && \
    rm -rf /var/lib/apt/lists/*

ENV PROTOBUF_ROOT=/opt/protobuf 
WORKDIR $PROTOBUF_ROOT 
 
 
ENV PROTOBUF_CLONE_TAG=v3.3.2 
RUN git clone -b ${PROTOBUF_CLONE_TAG} --depth 1 https://github.com/google/protobuf.git . && \ 
    ./autogen.sh && \ 
    ./configure && make -j"$(nproc)" && make check && make install && ldconfig 

ENV CAFFE_ROOT=/opt/caffe
WORKDIR $CAFFE_ROOT

# FIXME: clone a specific git tag and use ARG instead of ENV once DockerHub supports this.
ENV CLONE_TAG=rc4

RUN git clone -b ${CLONE_TAG} --depth 1 https://github.com/BVLC/caffe.git . && \
    pip3 install --upgrade pip && \ 
    cd python && for req in $(cat requirements.txt) pydot; do pip3 install $req; done && cd .. && \ 
    pip3 install python-dateutil --upgrade && \ 
    mkdir build && cd build && \
    cmake -DCPU_ONLY=1 -Dpython_version=3.. && \
    make -j"$(nproc)"

ENV PYCAFFE_ROOT $CAFFE_ROOT/python
ENV PYTHONPATH $PYCAFFE_ROOT:$PYTHONPATH
ENV PATH $CAFFE_ROOT/build/tools:$PYCAFFE_ROOT:$PATH
RUN echo "$CAFFE_ROOT/build/lib" >> /etc/ld.so.conf.d/caffe.conf && ldconfig

RUN pip3 install \ 
       virtualenv python-dotenv termcolor xmlrunner 

WORKDIR /workspace

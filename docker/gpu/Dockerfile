FROM nvidia/cuda:8.0-cudnn6-devel-ubuntu16.04
ENV LANG C.UTF-8
RUN groupadd --gid 1000 uisee \
    && useradd --uid 1000 --gid uisee --shell /bin/bash --create-home uisee

RUN sed -i 's/archive.ubuntu.com/mirrors.tuna.tsinghua.edu.cn/g' /etc/apt/sources.list && \
    apt-get update && apt-get install -y --no-install-recommends \
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
    libprotobuf-dev \
    libsnappy-dev \
    pkg-config \
    libgtk2.0-dev \
    pkg-config \
    libavcodec-dev \
    libavformat-dev \
    libswscale-dev \
    libjpeg-dev \
    libpng-dev \
    libtiff-dev \
    libjasper-dev \
    libdc1394-22-dev \
    protobuf-compiler \
    python3-dev \
    python3-setuptools && \
    rm -rf /var/lib/apt/lists/*

ENV CAFFE_ROOT=/opt/caffe
WORKDIR $CAFFE_ROOT

RUN curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py && \
    python3 get-pip.py && \
    pip3 install -i https://pypi.tuna.tsinghua.edu.cn/simple \
    Cython \
    wheel && \
    wget https://github.com/opencv/opencv/archive/3.4.7.tar.gz && \
    tar -zxf 3.4.7.tar.gz && cd 3.4.7 && \
    mkdir build && cd build && \
    cmake .. && make -j"$(nproc)" && make install \
    git clone -b 1.0 --depth 1 https://github.com/woodsgao/caffe.git . && \
    pip3 install -i https://pypi.tuna.tsinghua.edu.cn/simple --upgrade pip && \
    cd python && for req in $(cat requirements.txt) pydot; do pip3 -i https://pypi.tuna.tsinghua.edu.cn/simple install $req; done && cd .. && \
    git clone https://github.com/NVIDIA/nccl.git && cd nccl && make -j install && cd .. && rm -rf nccl && \
    mkdir build && cd build && \
    cmake -DUSE_CUDNN=1 -DUSE_NCCL=1 .. && \
    make -j"$(nproc)"

ENV PYCAFFE_ROOT $CAFFE_ROOT/python
ENV PYTHONPATH $PYCAFFE_ROOT:$PYTHONPATH
ENV PATH $CAFFE_ROOT/build/tools:$PYCAFFE_ROOT:$PATH
RUN echo "$CAFFE_ROOT/build/lib" >> /etc/ld.so.conf.d/caffe.conf && ldconfig

WORKDIR /workspace

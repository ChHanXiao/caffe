FROM ubuntu:16.04
ENV LANG C.UTF-8
RUN groupadd --gid 1000 uisee \
    && useradd --uid 1000 --gid uisee --shell /bin/bash --create-home uisee

RUN sed -i 's/archive.ubuntu.com/mirrors.tuna.tsinghua.edu.cn/g' /etc/apt/sources.list && \
    apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    cmake \
    git \
    wget \
    curl \
    ca-certificates \
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
    pip3 install -i https://pypi.tuna.tsinghua.edu.cn/simple --upgrade \
    Cython \
    wheel \
    python-dateutil \
    numpy && \
    wget https://github.com/opencv/opencv/archive/3.4.7.tar.gz && \
    tar -zxf 3.4.7.tar.gz && cd opencv-3.4.7 && \
    mkdir build && cd build && \
    cmake -D CMAKE_BUILD_TYPE=RELEASE \
    -D PYTHON_DEFAULT_EXECUTABLE=/usr/bin/python3 \
    -D PYTHON3_EXECUTABLE=/usr/bin/python3 \
    -D PYTHON3_NUMPY_INCLUDE_DIRS=/usr/local/lib/python3.5/dist-packages/numpy/core/include \
    -D PYTHON3_PACKAGES_PATH=/usr/lib/python3/dist-packages \
    -D CMAKE_CXX_FLAGS="-std=c++11" \
    -D CUDA_NVCC_FLAGS="--compiler-options "-std=c++03"" .. && \
    make -j"$(nproc)" && make install && rm -r $CAFFE_ROOT/*
ENV LD_LIBRARY_PATH /usr/local/lib:&LD_LIBRARY_PATH
RUN git clone --depth 1 https://github.com/woodsgao/caffe.git . && \
    mv Makefile.config.cpu Makefile.config && \
    cd python && for req in $(cat requirements.txt) pydot; do pip3 install -i https://pypi.tuna.tsinghua.edu.cn/simple $req; done && cd .. && \
    make -j"$(nproc)" all && \
    make -j"$(nproc)" test && \
    make -j"$(nproc)" pycaffe

ENV PYCAFFE_ROOT $CAFFE_ROOT/python
ENV PYTHONPATH $PYCAFFE_ROOT:$PYTHONPATH
ENV PATH $CAFFE_ROOT/build/tools:$PYCAFFE_ROOT:$PATH
RUN echo "$CAFFE_ROOT/build/lib" >> /etc/ld.so.conf.d/caffe.conf && ldconfig

WORKDIR /workspace

FROM nvidia/cuda:8.0-cudnn5-devel

RUN apt-get update && \
  apt-get -y dist-upgrade && \
  apt-get -y install \
  kmod \
  make \
  build-essential \
  cmake \
  cpp \
  g++ \
  gcc \
  libatlas-base-dev \
  curl \
  python-pip \
  openmpi-bin \
  libopenmpi-dev \
  libjsoncpp-dev \
  libhdf5-dev \
  openssh-client \
  zlib1g-dev

RUN pip install awscli
ENV GPU_DRIVER_VERSION=352.99
RUN cd /tmp && \
curl -LO http://us.download.nvidia.com/XFree86/Linux-x86_64/$GPU_DRIVER_VERSION/NVIDIA-Linux-x86_64-$GPU_DRIVER_VERSION.run && \
chmod +x ./NVIDIA-Linux-x86_64-$GPU_DRIVER_VERSION.run && \
./NVIDIA-Linux-x86_64-$GPU_DRIVER_VERSION.run -s --no-kernel-module && \
rm -rf /tmp/*

RUN ln -s /usr/include/hdf5/serial/* /usr/include/
RUN ln -s /usr/lib/x86_64-linux-gnu/hdf5/serial/libhdf5* /usr/lib/x86_64-linux-gnu/

RUN cd /tmp && \
curl -LO ftp://ftp.unidata.ucar.edu/pub/netcdf/netcdf-4.1.3.tar.gz && \
tar xvf netcdf-4.1.3.tar.gz && \
cd netcdf-4.1.3 && \
./configure --prefix=/usr/local && \
make -j16 && \
make install && rm -rf /tmp/*

RUN cd /tmp && \
curl -LO http://www.unidata.ucar.edu/downloads/netcdf/ftp/netcdf-cxx4-4.2.tar.gz && \
tar xvf netcdf-cxx4-4.2.tar.gz && \
cd netcdf-cxx4-4.2 && \
./configure --prefix=/usr/local && \
make -j16 && \
make install && rm -rf /tmp/*

RUN cd /tmp && \
curl -LO https://github.com/NVlabs/cub/archive/1.5.2.zip && \
apt-get install -y unzip && \
unzip 1.5.2.zip && \
cp -rf cub-1.5.2/cub/ /usr/local/include/ && \
rm -rf /tmp/*

ENV PATH=/usr/local/openmpi/bin/:/usr/local/cuda/bin/:${PATH}
ENV LD_LIBRARY_PATH=/usr/local/lib/:${LD_LIBRARY_PATH}
RUN ln -sf /usr/lib/openmpi /usr/local/openmpi
COPY src /opt/amazon/dsstne/src
RUN sed -i "s/-gencode arch=compute_60,code=sm_60//" /opt/amazon/dsstne/src/amazon/dsstne/Makefile.inc
RUN cd /opt/amazon/dsstne/src/amazon/dsstne && make
ENV PATH /opt/amazon/dsstne/src/amazon/dsstne/bin:${PATH}

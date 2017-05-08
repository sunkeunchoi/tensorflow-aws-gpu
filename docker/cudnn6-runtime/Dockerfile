FROM nvidia/cuda:8.0-cudnn6-devel

RUN apt-get update && apt-get -y dist-upgrade && apt-get install -y --no-install-recommends \
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
  zlib1g-dev \
  && \
  apt-get clean && \
  rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

RUN curl -fSsL -O https://bootstrap.pypa.io/get-pip.py && \
    python get-pip.py && \
    rm get-pip.py

RUN pip --no-cache-dir install --upgrade \
    awscli

#ENV GPU_DRIVER_VERSION=352.99
ENV GPU_DRIVER_VERSION=375.26 

RUN cd /tmp && \
curl -LO http://us.download.nvidia.com/XFree86/Linux-x86_64/$GPU_DRIVER_VERSION/NVIDIA-Linux-x86_64-$GPU_DRIVER_VERSION.run && \
chmod +x ./NVIDIA-Linux-x86_64-$GPU_DRIVER_VERSION.run && \
./NVIDIA-Linux-x86_64-$GPU_DRIVER_VERSION.run -s --no-kernel-module && \
rm -rf /tmp/*

RUN ln -s /usr/include/hdf5/serial/* /usr/include/
RUN ln -s /usr/lib/x86_64-linux-gnu/hdf5/serial/libhdf5* /usr/lib/x86_64-linux-gnu/

# Install netcdf
#curl -LO https://www.unidata.ucar.edu/downloads/netcdf/ftp/netcdf-4.1.3.tar.gz && \
    
RUN cd /tmp && \
    curl -LO https://github.com/Unidata/netcdf-c/archive/v4.4.1.1.tar.gz && \
    tar xvf netcdf-4.1.3.tar.gz && \
    cd netcdf-4.1.3 && \
    ./configure --prefix=/usr/local && \
    make -j16 && \
    make install && rm -rf /tmp/*

# Install netcdf-cxx
#curl -LO https://www.unidata.ucar.edu/downloads/netcdf/ftp/netcdf-cxx4-4.2.tar.gz && \
    
RUN cd /tmp && \
    curl -LO https://github.com/Unidata/netcdf-cxx4/archive/v4.3.0.tar.gz && \
    tar xvf netcdf-cxx4-4.2.tar.gz && \
    cd netcdf-cxx4-4.2 && \
    ./configure --prefix=/usr/local && \
    make && \
    make install && rm -rf /tmp/*


# Installing CUBG
RUN cd /tmp && \
    curl -LO https://github.com/NVlabs/cub/archive/1.5.2.zip && \
    unzip 1.5.2.zip && \
    cp -rf cub-1.5.2/cub/ /usr/local/include/ && \
    rm -rf /tmp/*

# Ensure OpenMPI is avaiable on path
ENV PATH=/usr/local/openmpi/bin/:${PATH} \
    LD_LIBRARY_PATH=/usr/local/lib/:${LD_LIBRARY_PATH}

RUN ln -sf /usr/lib/openmpi /usr/local/openmpi
#COPY src /opt/amazon/dsstne/src
#RUN sed -i "s/-gencode arch=compute_60,code=sm_60//" /opt/amazon/dsstne/src/amazon/dsstne/Makefile.inc
#RUN cd /opt/amazon/dsstne/src/amazon/dsstne && make
#ENV PATH /opt/amazon/dsstne/src/amazon/dsstne/bin:${PATH}
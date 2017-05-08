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
RUN ln -svi /usr/lib/x86_64-linux-gnu/hdf5/serial/libhdf5* /usr/lib/x86_64-linux-gnu/

# Install OpenMPI
ENV OPENMPI_VERSION 2.1.0
RUN cd /tmp  &&  \
    curl -LO http://www.open-mpi.org/software/ompi/v2.1/downloads/openmpi-$OPENMPI_VERSION.tar.gz && \
    tar xf openmpi-$OPENMPI_VERSION.tar.gz && \
    cd openmpi-$OPENMPI_VERSION && \
    ./configure --prefix=/usr/local/openmpi && \
    make && \
    sudo make install && rm -rf /tmp/*

# Install JSONCPP
RUN cd /tmp  && \
    curl -LO https://github.com/open-source-parsers/jsoncpp/archive/svn-import.tar.gz && \
    tar xf svn-import.tar.gz && \
    cd jsoncpp-svn-import && \
    mkdir -p build/release && \
    cd build/release && \
    cmake -DCMAKE_BUILD_TYPE=release -DJSONCPP_LIB_BUILD_SHARED=OFF -G "Unix Makefiles" ../.. && \
    make && \
    make install && rm -rf /tmp/*

# Install hdf5
ENV HDF5_VERSION 1.8.9
RUN cd /tmp && \
    curl -LO https://support.hdfgroup.org/ftp/HDF5/releases/hdf5-$HDF5_VERSION/src/hdf5-$HDF5_VERSION.tar.gz && \
    tar xf hdf5-$HDF5_VERSION.tar.gz && \
    cd hdf5-$HDF5_VERSION && \
    ./configure --prefix=/usr/local && \
    make && \
    make install && rm -rf /tmp/*

# Install zlib
#curl -LO https://www.unidata.ucar.edu/downloads/netcdf/ftp/netcdf-4/zlib-1.2.8.tar.gz && \
#http://zlib.net/zlib-1.2.11.tar.gz
ENV ZLIB_VERSION 1.2.11
RUN cd /tmp && \
    curl -LO http://zlib.net/zlib-$ZLIB_VERSION.tar.gz && \
    tar xf zlib-$ZLIB_VERSION.tar.gz && \
    cd zlib-$ZLIB_VERSION && \
    ./configure && \
    make && \
    make install && rm -rf /tmp/*



# Install netcdf
#curl -LO https://www.unidata.ucar.edu/downloads/netcdf/ftp/netcdf-4.1.3.tar.gz && \
#curl -LO https://github.com/Unidata/netcdf-c/archive/v4.4.1.1.tar.gz && \
#curl -LO ftp://ftp.unidata.ucar.edu/pub/netcdf/netcdf-4.4.1.1.tar.gz && \   
ENV NETCDF_VERSION 4.4.1.1
RUN cd /tmp && \
    curl -LO https://github.com/Unidata/netcdf-c/archive/v$NETCDF_VERSION.tar.gz && \
    tar xf v$NETCDF_VERSION.tar.gz && \
    cd netcdf-c-$NETCDF_VERSION && \
    ./configure --prefix=/usr/local && \
    make -j16 && \
    make install && rm -rf /tmp/*

# Install netcdf-cxx
#curl -LO https://www.unidata.ucar.edu/downloads/netcdf/ftp/netcdf-cxx4-4.2.tar.gz && \
#curl -LO https://github.com/Unidata/netcdf-cxx4/archive/v4.3.0.tar.gz && \ 
ENV NETCDF_CXX_VERSION 4.3.0  
RUN cd /tmp && \
    curl -LO https://github.com/Unidata/netcdf-cxx4/archive/v$NETCDF_CXX_VERSION.tar.gz && \
    tar xf v$NETCDF_CXX_VERSION.tar.gz && \
    cd netcdf-cxx4-$NETCDF_CXX_VERSION && \
    ./configure --prefix=/usr/local && \
    make && \
    make install && rm -rf /tmp/*


# Installing CUBG
#https://github.com/NVlabs/cub/archive/1.6.4.zip
ENV NVIDIA_CUB_VERSION 1.6.4
RUN cd /tmp && \
    curl -LO https://github.com/NVlabs/cub/archive/$NVIDIA_CUB_VERSION.zip && \
    unzip $NVIDIA_CUB_VERSION.zip && \
    cp -rf cub-$NVIDIA_CUB_VERSION/cub/ /usr/local/include/ && \
    rm -rf /tmp/*

# Ensure OpenMPI is avaiable on path
ENV PATH=/usr/local/openmpi/bin/:${PATH} \
    LD_LIBRARY_PATH=/usr/local/lib/:${LD_LIBRARY_PATH}

RUN ln -sf /usr/lib/openmpi /usr/local/openmpi
#COPY src /opt/amazon/dsstne/src
#RUN sed -i "s/-gencode arch=compute_60,code=sm_60//" /opt/amazon/dsstne/src/amazon/dsstne/Makefile.inc
#RUN cd /opt/amazon/dsstne/src/amazon/dsstne && make
#ENV PATH /opt/amazon/dsstne/src/amazon/dsstne/bin:${PATH}
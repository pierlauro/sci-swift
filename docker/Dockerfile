FROM ubuntu:18.04

RUN apt-get update && apt-get install -y autoconf git gcc g++ libtool make python3 python3-pip unzip vim wget

RUN ln -s /usr/bin/libtoolize /usr/bin/libtool

RUN wget http://www.mpich.org/static/downloads/3.3/mpich-3.3.tar.gz && tar xvfz mpich-3.3.tar.gz

WORKDIR mpich-3.3

RUN ./autogen.sh

RUN ./configure --enable-romio --enable-shared --with-device=ch3:sock --disable-fortran --enable-cxx --prefix=$(pwd)/build

RUN make -j `nproc` && make -j `nproc` test && make install

ENV PATH="/mpich-3.3/build/bin:${PATH}"

WORKDIR /

#RUN wget https://github.com/live-clones/hdf5/archive/hyperslab_updates.zip && unzip hyperslab_updates.zip
# Downloading snapshot version 1.9 release 235
RUN wget https://github.com/live-clones/hdf5/archive/e0b353b6f5a9c80ffe7a1f82cee6a15c13d25692.zip && unzip e0b353b6f5a9c80ffe7a1f82cee6a15c13d25692.zip

RUN mv hdf5-e0b353b6f5a9c80ffe7a1f82cee6a15c13d25692 hdf5-1.9.235

WORKDIR hdf5-1.9.235

RUN ./autogen.sh

RUN mkdir build

RUN ./configure --prefix=$PWD/build --enable-parallel --enable-shared 

RUN make -j `nproc` && make install
#RUN make check

ENV PATH="/hdf5-1.9.235/build/bin:${PATH}"

RUN pip3 install mpi4py numpy

WORKDIR /

RUN wget https://files.pythonhosted.org/packages/43/27/a6e7dcb8ae20a4dbf3725321058923fec262b6f7835179d78ccc8d98deec/h5py-2.9.0.tar.gz && tar xvfz h5py-2.9.0.tar.gz

WORKDIR h5py-2.9.0

RUN python3 setup.py configure --mpi --hdf5=/hdf5-hyperslab_updates/build

ENV CPATH=/mpich-3.3/build/include/:/hdf5-hyperslab_updates/build/include/

RUN python3 setup.py build

RUN python3 setup.py install

RUN mkdir /examples

COPY examples /examples

WORKDIR /examples

RUN mpicc mpi_hello_world.c

RUN mpiexec -n 4 ./a.out ## TODO check output

RUN h5pcc Hyperslab_by_row.c

RUN mpiexec -n 4 ./a.out # TODO check output

RUN mpiexec -n 4 python3 testh5py.py 

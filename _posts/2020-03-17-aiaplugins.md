---
title: "Build pvplugins of ZFS"
tags:
  - hpc
  - jsc
  - zfs
---

The ZFS uses a portable data format of the NetCDF. The coordinates of computational mesh,
instantaneous data of field variables, and all the post-processing data are saved in runtime via parallel
I/O functions defined in the ZFS modules. The parallel I/O modules are written in a pnetCDF regime.
The pnplugins building requires the ZFS source files, HDF5, pnetCDF, and ParaView.
Unfortunately the former pvplugins earlier than the revision 9xx are not compatible with the current
ZFS release which has been refined for multi-scale multi-physics simulations.
So far a variety of ZFS modules are under development for the new HPC systems,
e.g., [JUWELS][juwelslink] at the [JSC][jsc-link].


[jsc-link]: http://www.fz-juelich.de/ias/jsc/EN/Home/home_node.html "J&uuml;lich Supercomputing Centre"
[juwelslink]: https://www.fz-juelich.de/ias/jsc/EN/Expertise/Supercomputers/JUWELS/JUWELS_node.html "J&uuml;lich Wizard for European Leadership Science"

## OpenMPI

```shell
export PREFIX=

configure CC=gxx CXX=g++ FC=gfortran \
--enable-mpirun-prefix-by-default --prefix=$PREFIX
```
 
## HDF5

```shell
../configure CC=mpicc FC=mpifort CXX=mpicxx \
--enable-parallel --enable-fortran --with-pic --prefix=
```


## pnetCDF

```shell
export HDF5_DIR=/usr/local/hdf5

../configure CC=gcc CXX=g++ F77=gfortran F90=gfortran FC=gfortran \
MPICC=mpicc MPIF77=mpif77 MPIF90=mpif90 MPICXX=mpicxx \
CFLAGS="-I${HDF5_DIR}/include -O3 -fPIC -DPIC" \
CXXFLAGS="-I${HDF5_DIR}/include -O3 -fPIC -DPIC" \
FFLAGS="-I${HDF5_DIR}/include -O3 -fPIC" \
FCFLAGS="-I${HDF5_DIR}/include -O3 -fPIC" \
LDFLAGS="-L${HDF5_DIR}/lib64 -L$MPI_DIR/lib64" --prefix=
```

## FFTW

```shell
export PATH=/usr/local/openmpi/bin:$PATH
export LD_LIBRARY_PATH=/usr/local/openmpi/lib64:$LD_LIBRARY_PATH

../configure --enable-float --enable-shared --enable-openmp --enable-mpi \
CC=mpicc CXX=mpic++ FC=mpicfort F77=mpifort --prefix=
```

## NetCDF

```shell
export HDF5_DIR=/usr/local/hdf5

../configure CC=mpicc CXX=mpicxx F77=mpifort F90=mpifort FC=mpifort \
MPICC=mpicc MPIF77=mpif77 MPIF90=mpifort MPICXX=mpicxx \
CFLAGS="-I${HDF5_DIR}/include -O3 -fPIC -DPIC" \
CXXFLAGS="-I${HDF5_DIR}/include -O3 -fPIC -DPIC" \
FFLAGS="-I${HDF5_DIR}/include -O3 -fPIC" \
FCFLAGS="-I${HDF5_DIR}/include -O3 -fPIC" \
LDFLAGS="-L${HDF5_DIR}/lib64" --disable-dap --prefix=
```

---
title: "Performance Optimisation and Productivity Audits"
tags:
  - hpc
  - jsc
---

The Performance Optimisation and Productivity Centre of Excellence [POP CoE](https://pop-coe.eu/ "POP Webpage") in Computing Applications provides performance optimisation and productivity services for academic and industrial code(s) in all domains. In the POP analyst training on 18-21 Mar. 2019 at [JSC (Jülich Supercomputing Centre)][jsc-link], an HPC application was analyzed by [Scalasca][scalasca] as well as [Paraver][paraver] workflow. We detail the HPC code in performance metric, scalability, and parallel efficiency via following structure.

## Background

__Test-case description:__ The Lattice Boltzmann Method (LBM) based on a BGK model is used to solve a computational domain generated with a local refinement method at the boundaries. The total number of cells is ca. 50 mil. The application performance is measured in 100 time steps on 2 node (48 cores per node) which yields a global workload imbalance 0.000983516%.

__Machine description:__ 
[JUWELS][juwels] (Jülich Wizard for European Leadership Science)
consists of the 2271 standard and the 240 large-memory nodes
each of which possesses 48 cores of the <font face="monospace">Dual Intel Xeon Platinum 8168</font> processors. The
application is running under the <font face="monospace">Slurm</font> (Simple Linux Utility for Resource Management) Workload
Manager, a free open-source batch system. The resource on JUWELS is managed by the ParaStation
process management daemon.

__Compile software:__ <font face="monospace">GCC/8.2.0</font>, <font face="monospace">ParaStationMPI/5.2.1-1</font>, <font face="monospace">parallel-netcdf/1.10.0</font>, <font face="monospace">FFTW/3.3.8</font>, <font face="monospace">CMake/3.13.0</font>

```shell
module load GCC/8.2.0 ParaStationMPI/5.2.1-1 pscom/5.2.9-1 parallel-netcdf/1.10.0 FFTW/3.3.8 CMake/3.13.0
```

__Analysis tools:__ <font face="monospace">Score-P/4.1</font>, <font face="monospace">Scalasca/2.4</font>, <font face="monospace">PAPI/5.6.0</font>, <font face="monospace">Vampir/9.5.0</font>, <font face="monospace">Extrae/3.6.1</font>, <font face="monospace">Paraver/4.8.1</font>

__Note:__ <font face="monospace">Score-P</font> instrument with a runtime filtering collected the hardware counters <font face="monospace">PAPI_TOT_CYC</font>, <font face="monospace">PAPI_TOT_INS</font>, <font face="monospace">PAPI_RES_STL</font>.

```shell
export SCOREP_METRIC_PAPI=PAPI_TOT_INS,PAPI_TOT_CYC,PAPI_RES_STL
```

__Application name:__ _ZFS_ (Zonal Fluid Solver)

__Programming language:__ C++

__Programming model:__ MPI + OpenMP

__Source code available:__ yes

__Input data:__ Amazing Fluid Dynamics

__Performance study:__ Overall performance of Lattice Boltzmann Method (LBM) module and porting to GPU accelerator

## Support activities
Under the SiVeGCS project (SiVeGCS is an acronym for "Koordination und Sicherstellung der
weiteren Verfügbarkeit der Supercomputing Ressourcen des GCS im Rahmen der nationalen
Höchstleistungsrechner-Infrastruktur") the action is defined to support the supercomputer
users such that the coordination of the high-performance computing (HPC) resources of the
Gauss Centre for Supercomputing ([GCS](http://www.gauss-centre.eu)) successfully ensures the availability in the
framework of the national HPC infrastructure. The main objective of this national project
pursues the world class systems for science, economy, society, and politics. The results
achieved in the detailed actions can be exploited and transferred to the national
communities to increase the outreach to the general public. To realized these primary aims,
the user projects are supported by the efficient and the effective
methods which include the communication with the Jülich Supercomputing Centre ([JSC][jsc-link])
supporting team, the technical consulting, and the national and the international cooperation.

## Developer
In this reporting a performance measurement is presented for the _ZFS_ (Zonal Fluid
Solver) developed by the Institute of Aerodynamics at the RWTH Aachen University, Germany ([AIA](http://www.aia.rwth-aachen.de/)).
The SimLab Highly Scalable Fluids & Solids Engineering ([SLFSE](https://www.jara.org/en/fluids-solids-engineering)) performs to support in the fields of engineering science the users experienced in massively parallel systems regarding high scalability, memory optimization, programming of hierarchic computer architectures, and performance optimization on computer nodes.

## Application structure

![Vampir timeline](/assets/images/2019-03-25-fig-7.png "Vampir timeline")
![Vampir timeline](/assets/images/2019-03-25-fig-8.png "Vampir timeline zoom")

The trace result of [Scalasca][scalasca] analysis is illustrated for 24 MPI ranks with 4 threads using [Vampir][vampir].

![Paraver timeline](/assets/images/2019-03-25-fig-2.png "Paraver timeline")

This figure shows a [Paraver][paraver] timeline for the same simulation setup. The master threads has a little synchronization time in the initialization and the file IO at the beginning and the termination of the program. The OMP threads are ativated during the main iterations colored in blue.

![Paraver timeline](/assets/images/2019-03-25-fig-3.png "Paraver timeline zoom")

The timeline of two MPI ranks in ten iterations. Colors indicate the state at idle (black), running (blue),
waiting a message (red), scheduling and fork/join (yellow), wait/wait all (crimson), and immediate send (pink).

![Paraver timeline](/assets/images/2019-03-25-fig-4.png "Paraver timeline mpi")

The timeline of MPI calls for two MPI ranks in ten iterations.

## Focus of analysis






## Scalability information
Unfortunately in this training course we could not perform the full scale analysis. For the full scalability analysis on the JUWELS the _ZFS_ needs computer resources which would be provided in the next Optimization & Scaling Workshop. Nevertheless, in the former Scaling Workshop on [JUQUEEN][juqueen] the DG (Discontinuous Galerkin method) module was scaled up to 28672 nodes as shown in this figure.

![Strong scaling of _ZFS_](http://www.fz-juelich.de/ias/jsc/EN/Expertise/High-Q-Club/ZFS/scalingplot.png?__blob=poster)

The further information is available on the [_ZFS_ page](http://www.fz-juelich.de/ias/jsc/EN/Expertise/High-Q-Club/ZFS/_node.html "ZFS on JUQUEEN") of the [JSC High-Q club](http://www.fz-juelich.de/ias/jsc/EN/Expertise/High-Q-Club/_node.html "High-Q Club").

## Application efficiency

![A code profile instrumented by Score-P is presented by Cube](/assets/images/2019-03-25-fig-1.png "Performance report presented by Cube")

Performance metric - structure (call tree) - system resource analyzed by [Scalasca][scalasca]

The pure MPI setup achieves using a Score-P instrument the following scores for the main functions selected in the call-tree.

* Parallel efficiency 83%
  - Load balance 84%
  - Communication efficiency 98%
    * Serialization efficiency 98%
    * Transfer efficieny 100%

## Load balance

![Load balance 1](/assets/images/2019-03-25-fig-5.png "Load balance 1")

A large disparity in the computation load occurs at the function call which calculates the boundary condition of no-slip wall. The local values at the boundary edges are updated at each time step for their physical conditions implemented by numerical schemes.

![Load balance 2](/assets/images/2019-03-25-fig-6.png "Load balance 2")

This figure illustrates that the computation of the main function accounts for ca. 40% of the computing time with a mean computing time 29.6s per MPI rank.

<!--

## Serial performance

## Communications

## Summary and recommendations
-->

[jsc-link]: http://www.fz-juelich.de/ias/jsc/EN/Home/home_node.html "Jülich Supercomputing Centre"
[juwels]: http://www.fz-juelich.de/ias/jsc/EN/Expertise/Supercomputers/JUWELS/JUWELS_node.html "Jülich Wizard for European Leadership Science"
[juqueen]: http://www.fz-juelich.de/ias/jsc/EN/Expertise/Supercomputers/JUQUEEN/JUQUEEN_node.html "JUQUEEN system"
[scalasca]: http://www.scalasca.org/ "Scalasca"
[vampir]: https://vampir.eu/ "Vampir"
[paraver]: https://tools.bsc.es/paraver "Paraver"


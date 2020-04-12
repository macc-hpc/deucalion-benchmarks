# The repository contains the following benchmarks:

Where possible, intructions are heavily based on [Unified European Applications Benchmark Suite](https://repository.prace-ri.eu/git/UEABS/ueabs/)

- [ALYA](#alya)
- [CP2K](#cp2k)
- [GROMACS](#gromacs)
- [NEMO](#nemo)
- [SPECFEM3D](#specfem3d)
- [IO500](#io500)
- [HPCC](#hpcc)
- [OPENNMT-TF](#opennmt-tf)
- [RESNET-50](#resnet-50)

# ALYA

The Alya System is a Computational Mechanics code capable of solving different physics, each one with its own modelization characteristics, in a coupled way. Among the problems it solves are: convection-diffusion reactions, incompressible flows, compressible flows, turbulence, bi-phasic flows and free surface, excitable media, acoustics, thermal flow, quantum mechanics (DFT) and solid mechanics (large strain). ALYA is written in Fortran 90/95 and parallelized using MPI and OpenMP.

- Web site: https://www.bsc.es/computer-applications/alya-system
- Code download: https://repository.prace-ri.eu/ueabs/ALYA/2.1/Alya.tar.gz 
- Build and run instructions: https://github.com/macc-hpc/deucalion-benchmarks/blob/master/ALYA/README.md
- Test Case A: https://repository.prace-ri.eu/ueabs/ALYA/2.1/TestCaseA.tar.gz 
- Test Case B: https://repository.prace-ri.eu/ueabs/ALYA/2.1/TestCaseB.tar.gz 

# CP2K

CP2K is a freely available quantum chemistry and solid-state physics software package that can perform atomistic simulations of solid state, liquid, molecular, periodic, material, crystal, and biological systems. CP2K provides a general framework for different modelling methods such as DFT using the mixed Gaussian and plane waves approaches GPW and GAPW. Supported theory levels include DFTB, LDA, GGA, MP2, RPA, semi-empirical methods (AM1, PM3, PM6, RM1, MNDO, ...), and classical force fields (AMBER, CHARMM, ...). CP2K can do simulations of molecular dynamics, metadynamics, Monte Carlo, Ehrenfest dynamics, vibrational analysis, core level spectroscopy, energy minimisation, and transition state optimisation using NEB or dimer method.

CP2K is written in Fortran 2008 and can be run in parallel using a combination of multi-threading, MPI, and CUDA. All of CP2K is MPI parallelised, with some additional loops also being OpenMP parallelised. It is therefore most important to take advantage of MPI parallelisation, however running one MPI rank per CPU core often leads to memory shortage. At this point OpenMP threads can be used to utilise all CPU cores without suffering an overly large memory footprint. The optimal ratio between MPI ranks and OpenMP threads depends on the type of simulation and the system in question. CP2K supports CUDA, allowing it to offload some linear algebra operations including sparse matrix multiplications to the GPU through its DBCSR acceleration layer. FFTs can optionally also be offloaded to the GPU. Benefits of GPU offloading may yield improved performance depending on the type of simulation and the system in question.

- Web site: https://www.cp2k.org/
- Code download: https://github.com/cp2k/cp2k/releases
- [Build & run instructions, details about benchmarks]( https://github.com/macc-hpc/deucalion-benchmarks/blob/master/CP2K/README.md)
- Benchmarks:
 - [Test Case A](https://repository.prace-ri.eu/git/UEABS/ueabs/-/tree/master/cp2k/benchmarks/TestCaseA_H2O-512)
 - [Test Case B](https://repository.prace-ri.eu/git/UEABS/ueabs/-/tree/master/cp2k/benchmarks/TestCaseB_LiH-HFX)
 - [Test Case C](https://repository.prace-ri.eu/git/UEABS/ueabs/-/tree/master/cp2k/benchmarks/TestCaseC_H2O-DFT-LS)

# GROMACS

GROMACS is a versatile package to perform molecular dynamics, i.e. simulate the Newtonian equations of motion for systems with hundreds to millions of particles.

It is primarily designed for biochemical molecules like proteins, lipids and nucleic acids that have a lot of complicated bonded interactions, but since GROMACS is extremely fast at calculating the nonbonded interactions (that usually dominate simulations) many groups are also using it for research on non-biological systems, e.g. polymers.

GROMACS supports all the usual algorithms you expect from a modern molecular dynamics implementation, (check the online reference or manual for details), but there are also quite a few features that make it stand out from the competition:

- GROMACS provides extremely high performance compared to all other programs. A lot of algorithmic optimizations have been introduced in the code; we have for instance extracted the calculation of the virial from the innermost loops over pairwise interactions, and we use our own software routines to calculate the inverse square root. In GROMACS 4.6 and up, on almost all common computing platforms, the innermost loops are written in C using intrinsic functions that the compiler transforms to SIMD machine instructions, to utilize the available instruction-level parallelism. These kernels are available in either single and double precision, and in support all the different kinds of SIMD support found in x86-family (and other) processors.
- Also since GROMACS 4.6, we have excellent CUDA-based GPU acceleration on GPUs that have Nvidia compute capability >= 2.0 (e.g. Fermi or later)
- GROMACS is user-friendly, with topologies and parameter files written in clear text format. There is a lot of consistency checking, and clear error messages are issued when something is wrong. Since a C preprocessor is used, you can have conditional parts in your topologies and include other files. You can even compress most files and GROMACS will automatically pipe them through gzip upon reading.
- There is no scripting language – all programs use a simple interface with command line options for input and output files. You can always get help on the options by using the -h option, or use the extensive manuals provided free of charge in electronic or paper format.
- As the simulation is proceeding, GROMACS will continuously tell you how far it has come, and what time and date it expects to be finished.
- Both run input files and trajectories are independent of hardware endian-ness, and can thus be read by any version GROMACS, even if it was compiled using a different floating-point precision.
- GROMACS can write coordinates using lossy compression, which provides a very compact way of storing trajectory data. The accuracy can be selected by the user.
- GROMACS comes with a large selection of flexible tools for trajectory analysis – you won’t have to write any code to perform routine analyses. The output is further provided in the form of finished Xmgr/Grace graphs, with axis labels, legends, etc. already in place!
- A basic trajectory viewer that only requires standard X libraries is included, and several external visualization tools can read the GROMACS file formats.
- GROMACS can be run in parallel, using either the standard MPI communication protocol, or via our own “Thread MPI” library for single-node workstations.
- GROMACS contains several state-of-the-art algorithms that make it possible to extend the time steps is simulations significantly, and thereby further enhance performance without sacrificing accuracy or detail.
- The package includes a fully automated topology builder for proteins, even multimeric structures. Building blocks are available for the 20 standard aminoacid residues as well as some modified ones, the 4 nucleotide and 4 deoxinucleotide resides, several sugars and lipids, and some special groups like hemes and several small molecules.
- There is ongoing development to extend GROMACS with interfaces both to Quantum Chemistry and Bioinformatics/databases.
- GROMACS is Free Software, available under the GNU Lesser General Public License (LGPL), version 2.1. You can redistribute it and/or modify it under the terms of the LGPL as published by the Free Software Foundation; either version 2.1 of the License, or (at your option) any later version.

Instructions:

- Web site: http://www.gromacs.org/
- Download, build and run: https://github.com/macc-hpc/deucalion-benchmarks/blob/master/GROMACS/README.md.
- Test Case A: https://repository.prace-ri.eu/ueabs/GROMACS/1.2/GROMACS_TestCaseA.tar.gz
- Test Case B: https://repository.prace-ri.eu/ueabs/GROMACS/1.2/GROMACS_TestCaseB.tar.gz


# NEMO

NEMO (Nucleus for European Modelling of the Ocean) [22] is mathematical modelling framework for research activities and prediction services in ocean and climate sciences developed by European consortium. It is intended to be tool for studying the ocean and its interaction with the other components of the earth climate system over a large number of space and time scales. It comprises of the core engines namely OPA (ocean dynamics and thermodynamics), SI3 (sea ice dynamics and thermodynamics), TOP (oceanic tracers) and PISCES (biogeochemical process).
Prognostic variables in NEMO are the three-dimensional velocity field, a linear or non-linear sea surface height, the temperature and the salinity. 

In the horizontal direction, the model uses a curvilinear orthogonal grid and in the vertical direction, a full or partial step z-coordinate, or s-coordinate, or a mixture of the two. The distribution of variables is a three-dimensional Arakawa C-type grid for most of the cases.

The model is implemented in Fortran 90, with preprocessing (C-pre-processor). It is optimized for vector computers and parallelized by domain decomposition with MPI. It supports modern C/C++ and Fortran compilers. All input and output is done with third party software called XIOS with dependency on NetCDF (Network Common Data Format) and HDF5. It is highly scalable and perfect application for measuring supercomputing performances in terms of compute capacity, memory subsystem, I/O and interconnect performance.

### Test Case Description 

The GYRE configuration has been built to model seasonal cycle of double gyre box model. It consists of idealized domain over which seasonal forcing is applied. This allows for studying large number of interactions and their combined contribution to large scale circulation.
The domain geometry is rectangular bounded by vertical walls and flat bottom. The configuration is meant to represent idealized north Atlantic or north pacific basin. The circulation is forced by analytical profiles of wind and buoyancy fluxes. 

The wind stress is zonal and its curl changes sign at 22 and 36. It forces a subpolar gyre in the north, a subtropical gyre in the wider part of the domain and a small recirculation gyre in the southern corner. The net heat flux takes the form of a restoring toward a zonal apparent air temperature profile.

A portion of the net heat flux which comes from the solar radiation is allowed to penetrate within the water column. The fresh water flux is also prescribed and varies zonally. It is determined such as, at each time step, the basin-integrated flux is zero.
The basin is initialized at rest with vertical profiles of temperature and salinity uniformity applied to the whole domain. The GYRE configuration is set through the namelist_cfg file. 

The horizontal resolution is determined by setting jp_cfg as follows:

`Jpiglo = 30 x jp_cfg + 2`

`Jpjglo = 20 x jp_cfg + 2`

In this configuration, we use default value of 30 ocean levels depicted by jpk=31. The GYRE configuration is an ideal case for benchmark test as it is very simple to increase the resolution and perform both weak and strong scalability experiment using the same input files. We use two configurations as follows:

**Test Case A**:

*	jp_cfg = 128 suitable up to 1000 cores
*	Number of Days: 20
*	Number of Time steps: 1440
*	Time step size: 20 mins
*	Number of seconds per time step: 1200


**Test Case B**

*	jp_cfg = 256 suitable up to 20,000 cores.
*	Number of Days (real): 80 
*	Number of time step: 4320
*	Time step size(real): 20 mins
*	Number of seconds per time step: 1200


* Web site: <http://www.nemo-ocean.eu/>
* Download, Build and Run Instructions : https://github.com/macc-hpc/deucalion-benchmarks/blob/master/NEMO/README.md


# SPECFEM3D

The software package SPECFEM3D simulates three-dimensional global and regional seismic wave propagation based upon the spectral-element method (SEM). All SPECFEM3D_GLOBE software is written in Fortran90 with full portability in mind, and conforms strictly to the Fortran95 standard. It uses no obsolete or obsolescent features of Fortran77. The package uses parallel programming based upon the Message Passing Interface (MPI).

The SEM was originally developed in computational fluid dynamics and has been successfully adapted to address problems in seismic wave propagation. It is a continuous Galerkin technique, which can easily be made discontinuous; it is then close to a particular case of the discontinuous Galerkin technique, with optimized efficiency because of its tensorized basis functions. In particular, it can accurately handle very distorted mesh elements. It has very good accuracy and convergence properties. The spectral element approach admits spectral rates of convergence and allows exploiting hp-convergence schemes. It is also very well suited to parallel implementation on very large supercomputers as well as on clusters of GPU accelerating graphics cards. Tensor products inside each element can be optimized to reach very high efficiency, and mesh point and element numbering can be optimized to reduce processor cache misses and improve cache reuse. The SEM can also handle triangular (in 2D) or tetrahedral (3D) elements as well as mixed meshes, although with increased cost and reduced accuracy in these elements, as in the discontinuous Galerkin method.

In many geological models in the context of seismic wave propagation studies (except for instance for fault dynamic rupture studies, in which very high frequencies of supershear rupture need to be modeled near the fault, a continuous formulation is sufficient because material property contrasts are not drastic and thus conforming mesh doubling bricks can efficiently handle mesh size variations. This is particularly true at the scale of the full Earth. Effects due to lateral variations in compressional-wave speed, shear-wave speed, density, a 3D crustal model, ellipticity, topography and bathyletry, the oceans, rotation, and self-gravitation are included. The package can accommodate full 21-parameter anisotropy as well as lateral variations in attenuation. Adjoint capabilities and finite-frequency kernel simulations are also included.

- Web site: http://geodynamics.org/cig/software/specfem3d_globe/
- Code download: http://geodynamics.org/cig/software/specfem3d_globe/
- Build instructions: http://www.geodynamics.org/wsvn/cig/seismo/3D/SPECFEM3D_GLOBE/trunk/doc/USER_MANUAL/manual_SPECFEM3D_GLOBE.pdf?op=file&rev=0&sc=0
- Test Case A: https://repository.prace-ri.eu/git/UEABS/ueabs/tree/r2.1-dev/specfem3d/test_cases/SPECFEM3D_TestCaseA
- Test Case B: https://repository.prace-ri.eu/git/UEABS/ueabs/tree/r2.1-dev/specfem3d/test_cases/SPECFEM3D_TestCaseB
- Download, build and run instructions: https://github.com/macc-hpc/deucalion-benchmarks/blob/master/SPECFEM3D/README.md

# IO500

The IO-500 has been developed together with the community and its development is still ongoing. The benchmark is essentially a benchmark suite bundled with execution rules. 
It harnesses existing and trusted open source benchmarks.

The goal for the benchmark is to capture user-experienced performance. It aims to be:

---------------------------------------------------

- Representative
- Understandable
- Scalable
- Portable
- Inclusive
- Lightweight
- Trustworthy

The benchmark covers various workloads and computes a single score for comparison. The workloads are:

- IOEasy: Applications with well optimized I/O patterns
- IOHard: Applications that require a random workload
- MDEasy: Metadata/small objects
- MDHard: Small files (3901 bytes) in a shared directory
- Find: Finding relevant objects based on patterns

The individual performance numbers are preserved and accessible via the web or the raw data. This allows deriving other relevant metrics.

- Web site: https://www.vi4io.org/io500/start
- Code download: https://github.com/VI4IO/io-500-dev
- Build and run instructions: https://github.com/macc-hpc/deucalion-benchmarks/blob/master/IO500/README.md

# HPCC

HPC Challenge is a benchmark suite that measures a range memory access patterns. Source code repository of the benchmark is located at HPCC SourceForge and HPCC@BitBucket pages.

The HPC Challenge benchmark consists of basically 7 tests:

- HPL - the Linpack TPP benchmark which measures the floating point rate of execution for solving a linear system of equations.
- DGEMM - measures the floating point rate of execution of double precision real matrix-matrix multiplication.
- STREAM - a simple synthetic benchmark program that measures sustainable memory bandwidth (in GB/s) and the corresponding computation rate for simple vector kernel.
- PTRANS (parallel matrix transpose) - exercises the communications where pairs of processors communicate with each other simultaneously. It is a useful test of the total communications capacity of the network.
- RandomAccess - measures the rate of integer random updates of memory (GUPS).
- FFT - measures the floating point rate of execution of double precision complex one-dimensional Discrete Fourier Transform (DFT).
- Communication bandwidth and latency - a set of tests to measure latency and bandwidth of a number of simultaneous communication patterns; based on b_eff (effective bandwidth benchmark).
---------------------------------------------------
- Web site: https://icl.utk.edu/hpcc/
- Code download: http://icl.cs.utk.edu/projectsfiles/hpcc/download/hpcc-1.5.0.tar.gz
- Build and run instructions: https://github.com/macc-hpc/deucalion-benchmarks/blob/master/HPCC/README.md


# OPENNMT-TF

OpenNMT-tf is a general purpose sequence learning toolkit using TensorFlow 2.0. While neural machine translation is the main target task, it has been designed to more generally support:

- sequence to sequence mapping
- sequence tagging
- sequence classification
- language modeling
---------------------------------------------------
- Web site: https://github.com/OpenNMT/OpenNMT-tf
- Download and run instructions: https://github.com/OpenNMT/OpenNMT-tf/tree/master/scripts/wmt

# RESNET-50

Resnet-50 v1 applied to Imagenet.

- Web site: https://github.com/mlperf/training
- Download and run instructions: https://github.com/mlperf/training/tree/master/image_classification

# Building Alya

In order to build ALYA (Alya.x), please follow these steps:

- Go to the directory: Executables/unix
- Build the Metis library (libmetis.a) using "make metis4"
- Adapt the file: configure.in to your own MPI wrappers and paths (examples on the configure.in fold$
- Execute:
  ./configure -x nastin parall
  make

Data sets
---------

The parameters used in the datasets try to represent at best typical industrial runs in order to obtain representative speedups. For example, the iterative solvers are never converged to machine accuracy, but only as a percentage of the initial residual. 

The different datasets are:

SPHERE_16.7M ... 16.7M sphere mesh 
SPHERE_132M .... 132M sphere mesh

How to execute Alya with a given dataset
----------------------------------------

In order to run ALYA, you need at least the following input files per execution:

X.dom.dat
X.ker.dat
X.nsi.dat
X.dat

In our case X=sphere

To execute a simulation, you must be inside the input directory and you should submit a job like:

mpirun Alya.x sphere

How to measure the speedup
--------------------------

There are many ways to compute the scalability of Nastin module.

1. For the complete cycle including: element assembly + boundary assembly + subgrid scale assembly + solvers, etc.

2. For single kernels: element assembly, boundary assembly, subgrid scale assembly, solvers

3. Using overall times


1. In *.nsi.cvg file, column "30. Elapsed CPU time"


2. Single kernels. Here, average and maximum times are indicated in *.nsi.cvg at each iteration of each time step:

Element assembly: 19. Ass. ave cpu time    20. Ass. max cpu time    

Boundary assembly: 33. Bou. ave cpu time  34. Bou. max cpu time   

Subgrid scale assembly: 31. SGS ave cpu time     32. SGS max cpu time

Iterative solvers: 21. Sol. ave cpu time     22. Sol. max cpu time   

Note that in the case of using Runge-Kutta time integration (the case of the sphere), the element and boundary assembly times are this of the last assembly of current time step (out of three for third order). 

3. At the end of *.log file, total timings are shown for all modules. In this case we use the first value of the NASTIN MODULE.




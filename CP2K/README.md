## Overview 
This README together with the provided benchmark input files, CP2K build configuration ("arch") files and other details provided in each subdirectory corresponding to a machine specify how the CP2K benchmarking results presented in the PRACE-5IP deliverable D7.5 ("Evaluation of Accelerated and Non-accelerated Benchmarks") were obtained and provide general guidance on building CP2K and running the UEABS benchmarks.

In short, the procedure followed to generate CP2K UEABS benchmark results for the D7.5 deliverable on a given machine was:

1. Compile Libint
2. Compile Libxc 
3. Compile FFTW library (or use MKL's FFTW3 interface)
4. Compile CP2K and link to Libint, Libxc, FFTW, LAPACK, BLAS, SCALAPACK and BLACS, and to relevant CUDA libraries if building for GPU
5. Run the benchmarks, namely:
 - Test Case A: H2O-1024 
 - Test Case B: LiH-HFX (adjusting the MAX_MEMORY input parameter to take into account available on-node memory)
 - Test Case C: H2O-DFT-LS 

Gcc/gfortran was used to compile the libraries and CP2K itself, with the exception of the Frioul machine on which the Intel compiler was used to compile for the Knights Landing processor. In general it is recommended to use an MPI library built using the same compiler used to build CP2K. General information about building CP2K and libraries it depends on can be found in INSTALL.md included in the CP2K source distribution.

The reported walltime for a given run is obtained by querying the resulting .log file for CP2K's internal timing, as follows:

```
grep "CP2K     "  *.log
```

### Optional
Optional performance libraries including ELPA, libgrid, and libsmm/libxsmm can and should be built and linked to when compiling a CP2K executable for maximum performance for production usage. This was not done for the results presented in deliverable D7.5 due to the effort and complexity involved in doing so for the range of machines on which benchmark results were generated, which included PRACE Tier-0 production machines as well as pre-production prototypes. Information about these libraries can be found in INSTALL.md included in the CP2K source distribution. 



## 1. Compile Libint
The Libint library can be obtained from <http://sourceforge.net/projects/libint/files/v1-releases/libint-1.1.4.tar.gz> 

The specific commands used to build version 1.1.4 of Libint using GCC on a number of different machines can be found in the machine-specific subdirectories accompanying this README. By default the build process only creates static (.a) libraries. If you want to be able to link dynamically to Libint when building CP2K you can pass the flag
--enable-shared to ./configure in order to produce shared libraries (.so). 

If you can, it is easiest to build Libint on the same processor architecture on which you will run CP2K. This typically correspond to being to compile directly on the relevant compute nodes of the machine. If this is not possible and if you are forced instead to compile on nodes with a different processor architecture to the compute nodes on which CP2K will eventually run, see the section below on cross-compiling Libint. 

More information about Libint can be found inside the CP2K distribution base directory in

```
/tools/hfx_tools/libint_tools/README_LIBINT
```

### Cross-compiling Libint for compute nodes
If you are forced to cross-compile Libint for compute nodes on nodes that have a different processor architecture, follow these instructions. They assume you will be able to call a parallel application launcher like ``mpirun`` or ``mpiexec`` during your build process in order to run compiled code.

In ``/src/lib/`` edit the files ``MakeRules`` and ``MakeRules.in``.  
On the last line of each file, replace

```
cd $(LIBSRCLINK); $(TOPOBJDIR)/src/bin/$(NAME)/$(COMPILER)
```
with

```
cd $(LIBSRCLINK); $(PARALLEL_LAUNCHER_COMMAND) $(TOPOBJDIR)/src/bin/$(NAME)/$(COMPILER)
```

Then run

```
export PARALLEL_LAUNCHER_COMMAND="mpirun -n 1"
```
replacing ``mpirun`` with a different parallel application launcher such as ``mpiexec`` (or ``aprun`` if applicable). When proceeding with the configure stage, include the configure flag ``cross-compiling=yes``.


## 2. Compile Libxc
The Libxc library can be obtained from <https://tddft.org/programs/libxc/download/previous/>. Version 4.2.3 was used for deliverable D7.5. 

The specific commands used to build Libxc using GCC on a number of different machines can be found in the machine-specific subdirectories accompanying this README.

## 3. Compile FFTW

If FFTW (FFTW3) is not already available preinstalled on your system it can be obtained from <http://www.fftw.org/download.html>

The specific commands used to build FFTW using GCC on a number of different machines can be found in the machine-specific subdirectories accompanying this README.

Alternatively, you can use MKL's FFTW3 interface. 


## 4. Compile CP2K
The CP2K source code can be downloaded from <https://github.com/cp2k/cp2k/releases/>. Version 6.1 of CP2K was used to generate the results reported in D7.5.

The general procedure is to create a custom so-called arch (architecture) file inside the ``arch`` directory in the CP2K distribution, which includes examples for a number of common architectures. The arch file specifies build parameters such as the choice of compilers, library locations and compilation and linker flags. Building the hybrid MPI + OpenMP ("psmp") version of CP2K (most convenient for running benchmarks) in accordance with a given arch file is then accomplished by entering the ``makefiles`` directory in the distribution and running

```
make -j number_of_cores_available_to_you ARCH=arch_file_name VERSION=psmp
```

If the build is successful, the resulting executable ``cp2k.psmp`` can be found inside ``/exe/arch_file_name/`` in the CP2K base directory.

Detailed information about arch file and library options and overall build procedure can be found in the ``INSTALL.md`` readme file. You can also consult https://dashboard.cp2k.org, which provides sample arch files as part of
the testing reports for some platforms (click on the status field for a platform, and search for 'ARCH-file' in the resulting output). 

Specific arch files used to build CP2K for deliverable D7.5 can be found in the machine-specific subdirectories in this repository. 

### Linking to MKL

If you are linking to Intel's MKL library to provide LAPACK, BLAS, SCALAPACK and BLACS (and possibly FFTW3) you should choose linking options using the [MKL Link Line Advisor tool](https://software.intel.com/en-us/articles/intel-mkl-link-line-advisor), carefully selecting the options relevant on your machine environment. 

### Building a CUDA-enabled version of CP2K

See the ``PizDaint`` subdirectory for an example arch file that enables GPU acceleration with CUDA. The ``-arch`` NVIDIA flag should be adjusted to choose the right option for the particular Nvidia GPU architecture in question. For example ``-arch sm35`` matches the Tesla K40 and K80 GPU architectures. 


## 5. Running the UEABS benchmarks
**The exact input files used to generate the results presented in deliverable D7.5 are included in machine-specific subdirectories accompanying this README**

After building the hybrid MPI+OpenMP version of CP2K you have an executable ending in `.psmp`. The general way to run the benchmarks with the hybrid parallel executable is, e.g. for 2 threads per rank:

```
export OMP_NUM_THREADS=2
parallel_launcher launcher_options path_to_cp2k.psmp -i inputfile -o logfile
```

Where:

- The parallel_launcher is mpirun, mpiexec, or some variant such as aprun on Cray systems or srun when using Slurm. 

- launcher\_options specifies parallel placement in terms of total numbers of nodes, MPI ranks/tasks, tasks per node, and OpenMP threads per task (which should be equal to the value given to OMP\_NUM\_THREADS). This is not necessary if parallel runtime options are picked up by the launcher from the job environment.

You can try any combination of tasks per node and OpenMP threads per task to investigate absolute performance and scaling on the machine of interest. 

### Test Case A: H2O-512

Test Case A is the H2O-512 benchmark included in the CP2K distribution (in ``/tests/QS/benchmark/``) and consists of ab-initio molecular dynamics simulation of 512 liquid water molecules using the Born-Oppenheimer approach via Quickstep DFT. 

### Test Case B: LiH-HFX

Test Case B is the LiH-HFX benchmark included in the CP2K distribution (in ``/tests/QS/benchmark_HFX/LiH``) and consists of a DFT energy calculation for a 216 atom LiH crystal. 

Provided input files include:

- ``input_bulk_HFX_3.inp``: input for the actual benchmark HFX run
- ``input_bulk_B88_3.inp``: needed to generate an initial wave function (wfn file) for the HFX run - this should be run once before running the actual HFX benchmark

After the B88 run has been performed once, you should rename the resulting wavefunction file for use in the HFX benchmark runs as follows:

```
cp LiH_bulk_3-RESTART.wfn B88.wfn
```

#### Notes
The amount of memory available per MPI process must be altered according to the number of MPI processes being used. If this is not done the benchmark will crash with an out of memory (OOM) error. The input file keyword ``MAX_MEMORY`` in ``input_bulk_HFX_3.inp`` needs to be changed as follows:  

```
MAX_MEMORY 14000
```  

should be changed to  

```
MAX_MEMORY new_value
```  

The new value of ``MAX_MEMORY`` is chosen by dividing the total amount of memory available on a node by the number of MPI processes being used per node. 

If a shorter runtime is desirable, the following line in ``input_bulk_HFX_3.inp``:  

```
MAX_SCF 20
```  

may be changed to  

```
MAX_SCF 1
```  

in order to reduce the maximum number of SCF cycles and hence the execution time. 

If the runtime or required memory needs to be reduced so the benchmark can run on a smaller number of nodes, the OPT1 basis set can be used instead of the default OPT2. To this end, the line  

```
BASIS_SET OPT2
```  

in ``input_bulk_B88_3.inp`` and in ``input_bulk_HFX_3.inp`` should be changed to  
  
```
BASIS_SET OPT1
```  

### Test Case C: H2O-DFT-LS

Test Case C is the H2O-DFT-LS benchmark included in the CP2K distribution (in ``tests/QS/benchmark_DM_LS``) and consists of a single-point energy calculation using linear-scaling DFT of 2048 water molecules. 

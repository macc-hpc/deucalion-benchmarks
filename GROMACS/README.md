# Download
Gromacs can be downloaded from : http://www.gromacs.org/Downloads
The UEABS benchmark cases require the use of 4.6 or newer branch,
the latest 4.6.x version is suggested.

# Build

Complete Build instructions: http://manual.gromacs.org/documentation/
A typical build procedure look like :
tar -zxf gromacs-2016.tar.gz
cd gromacs-2016
mkdir build
cd build
cmake \
        -DCMAKE_INSTALL_PREFIX=$HOME/Packages/gromacs/2016 \
        -DBUILD_SHARED_LIBS=off \
        -DBUILD_TESTING=off \
        -DREGRESSIONTEST_DOWNLOAD=OFF \
        -DCMAKE_C_COMPILER=`which mpicc` \
        -DCMAKE_CXX_COMPILER=`which mpicxx` \
        -DGMX_BUILD_OWN_FFTW=on \
        -DGMX_SIMD=AVX2_256 \
        -DGMX_DOUBLE=off \
        -DGMX_EXTERNAL_BLAS=off \
        -DGMX_EXTERNAL_LAPACK=off \
        -DGMX_FFT_LIBRARY=fftw3 \
        -DGMX_GPU=off \
        -DGMX_MPI=on \
        -DGMX_OPENMP=on \
        -DGMX_X11=off \
        ..
make (or make -j ##)
make install
        
You probably need to adjust 
1. The CMAKE_INSTALL_PREFIX to point to a different path
2. GMX_SIMD : You may completely ommit this if your compile and compute nodes are of the same architecture (for example Haswell).
   If they are different you should specify what fits to your compute nodes.
   For a complete and up to date list of possible choices refer to the gromacs official build instructions.

# Run


There are two data sets in UEABS for Gromacs.
1. ion_channel that use PME for electrostatics, for Tier-1 systems
2. lignocellulose-rf that use Reaction field for electrostatics, for Tier-0 systems. Reference :  http://pubs.acs.org/doi/abs/10.1021/bm400442n
The input data file for each benchmark is the corresponding .tpr file produced using
tools from a complete gromacs installation and a series of ascii data files 
(atom coords/velocities, forcefield, run control).
If it happens to run the tier-0 case on BG/Q use lignucellulose-rf.BGQ.tpr
instead lignocellulose-rf.tpr. It is the same as lignocellulose-rf.tpr 
created on a BG/Q system.
The general way to run gromacs benchmarks is :
WRAPPER WRAPPER_OPTIONS PATH_TO_GMX mdrun -s CASENAME.tpr -maxh 0.50 -resethway -noconfout -nsteps 10000 -g logile
CASENAME is one of ion_channel or lignocellulose-rf
maxh        : Terminate after 0.99 times this time (hours) i.e. gracefully terminate after ~30 min
resethwat   : Reset Timer counters at half steps. This means that the reported
	      walltime and performance referes to the last 
              half steps of sumulation.
noconfout   : Do not save output coordinates/velocities at the end.
nsteps      : Run this number of steps, no matter what is requested in the input file
logfile     : The output filename. If extension .log is ommited 
	      it is automatically appended. Obviously, it should be different
	      for different runs.
WRAPPER and WRAPPER_OPTIONS  depend on system, batch system etc.
Few common pairs are :
CRAY     : aprun -n TASKS -N NODES -d THREADSPERTASK
Curie    : ccc_mrun with no options - obtained from batch system
Juqueen  : runjob --np TASKS --ranks-per-node TASKSPERNOD --exp-env OMP_NUM_THREADS
Slurm    : srun  with no options, obtained from slurm if the variables below are set.
#SBATCH --nodes=NODES
#SBATCH --ntasks-per-node=TASKSPERNODE
#SBATCH --cpus-per-task=THREADSPERTASK
The best performance is usually obtained using pure MPI i.e. THREADSPERTASK=1.
You can check other hybrid MPI/OMP combinations.
The execution time  is reported at the end of logfile : grep Time: logfile | awk -F ' ' '{print $3}'
NOTE : This is the wall time for the last half number of steps. 
       For sufficiently large nsteps, this is half of the total wall time.

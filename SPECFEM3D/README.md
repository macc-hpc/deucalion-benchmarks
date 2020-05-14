# SPECFEM3D Instructions

## Get the source code

```
git clone --recursive https://github.com/geodynamics/specfem3d_globe.git
git clone https://repository.prace-ri.eu/git/UEABS/ueabs.git

cd specfem3d_globe

# checkout latest stable (as of April 2019)
git checkout -b stable tags/v7.0.2
cd ..
```

## Load environment modules

You will need a fortran and a C compiler and a MPI library.
The following variables are relevant to compile the code in case you need specific compilers (the configure script should deal with what's already in the environment):

* CC
* FC
* MPIFC

```
# load the environment modules (versions are for sake of example)
ml purge
ml gcc/7.4.0 openmpi/3.1.5
```

## Prepare the test cases, configure and build

As arrays are statically declared, you will need to compile specfem3d once for each
test case with the right Par_file. Indeed, input for the mesher (and the solver) is provided through the parameter file Par_file, which resides in the
subdirectory DATA of specfem3D_Globe.

Also, keep in mind that the configure step overwrites the Par_file. Be sure to copy it before the make steps.

```
test_case=TestCaseA
cp -r specfem3d_globe specfem3d_globe_${test_case}
cd specfem3d_globe_${test_case}/

# use appropriate flags for your system
./configure CFLAGS="-g -O3 -mtune=sandybridge" FCFLAGS="-g -O3 -mtune=sandybridge" --prefix=$PWD --enable-openmp

cp ../ueabs/specfem3d/test_cases/SPECFEM3D_${test_case}/* DATA/

make clean
make all -j 8
```

## Run

To run the test cases:

1. Copy the Par_file, STATIONS and CMTSOLUTION files from ueabs/specfem3d/test_cases/SPECFEM3D_TestCaseX into the SPECFEM3D_GLOBE/DATA directory.
2. Recompile the mesher and the solver.
3. Run the mesher and the solver.

SPECFEM3D_TestCaseA requires 96 MPI tasks and SPECFEM3D_TestCaseB 1536 MPI tasks. Be sure to spread tasks and leverage threads according to your system specifications.

Example Slurm Batch (TestCaseA):

```
#!/bin/bash

#SBATCH --job-name=specfem_teste_case_a
#SBATCH --output=output.out
#SBATCH --error=output.err
#SBATCH --nodes=24
#SBATCH --ntasks-per-node=4
#SBATCH --ntasks-per-core=1
#SBATCH --cpus-per-task=4
#SBATCH --time=01:00:00

set -e

export OMP_NUM_THREADS=$SLURM_CPUS_PER_TASK
export OMP_NUM_THREADS=4
export OMP_PLACES=threads
export OMP_PROC_BIND=true
export OMP_SCHEDULE=dynamic

ulimit -s unlimited

MESHER_EXE=./bin/xmeshfem3D
SOLVER_EXE=./bin/xspecfem3D

##
## mesh generation
##
sleep 2

echo
echo `date`
echo "starting MPI mesher"
echo

MPI_PROCESS=` echo "$SLURM_NNODES*$SLURM_NTASKS_PER_NODE" | bc -l`
echo "SLURM_NTASKS_PER_NODE = " $SLURM_NTASKS_PER_NODE
echo "SLURM_CPUS_PER_TASKS = " $SLURM_CPUS_PER_TASK
echo "SLURM_NNODES=" $SLURM_NNODES
echo "MPI_PROCESS $MPI_PROCESS"
echo "OMP_NUM_THREADS=$OMP_NUM_THREADS"

time srun ${MESHER_EXE}
echo "  mesher done: `date`"
echo

##
## forward simulation
##
sleep 2

echo
echo `date`
echo starting run in current directory $PWD
echo
time srun ${SOLVER_EXE}

echo "finished successfully"
echo `date`
```

## Gather results

The relevant metric for this benchmark is time for the solver, you can find it at the end of this output file : specfem3d_globe_${test_case}/OUTPUT_FILES/output_solver.txt.

Other options are timing each run with the `time` command or using Slurm accounting to observe setep duration.
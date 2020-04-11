
# Build 
# Load modules
ml gcc/7.4.0 openmpi

cp -r specfem3d_globe specfem3d_globe_test_case_a
cp -r ueabs/specfem3d/test_cases/SPECFEM3D_TestCaseA specfem3d_globe_test_case_a/DATA/
cd specfem3d_globe_test_case_a/

./configure --prefix=$PWD --enable-openmp
export CFLAGS=" -g -O3 -mtune=sandybridge -ipo”
export FCFLAGS=" -g -O3 -qopenmp -mtune=sandybridge -ipo -DUSE_FP32 -DOPT_STREAMS -fp-model fast=2 -traceback -mcmodel=large”
make clean
make all
```

# Run
To run the test cases, copy the Par_file, STATIONS and CMTSOLUTION files into the SPECFEM3D_GLOBE/DATA directory.
Recompile the mesher and the solver.
Run the mesher and the solver.

Example:
```
mpirun -n 4 bin/xmeshfem3D
```
SPECFEM3D_TestCaseA runs on 864 cores and SPECFEM3D_TestCaseB runs on 11616 cores.


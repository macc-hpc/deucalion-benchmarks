# Build 

Create a file Make.<arch> in the  top-level directory. For this purpose,  you  may  want  to re-use one contained in the setup directory. 
This file essentially contains the compilers and libraries with their paths to be used.
Type "make arch=<arch>". This  should create an executable in the bin/<arch> directory called xhpl.

For example, on our Linux PII cluster, I create a file called Make.Linux_PII in the top-level directory. Then, I type
"make arch=Linux_PII" 
This creates the executable file bin/Linux_PII/xhpl.


```
cd hpl

# Create a Makefile similar to the ones in hpl/setup.

make arch=Linux_PII # Example on setup directory.
```

# Run

Run some tests:

```
cd bin/<arch>
mpirun -np 4 xhpl
```
Most of the performance  parameters can be tuned, by modifying the input file bin/HPL.dat. See the file TUNING in the top-level directory.



# Download
Get code from Github:
```
git clone https://github.com/VI4IO/io-500-dev.git -b io500-sc-19
```


This is designed to work from an interactive login (e.g. it won't work from a login node) and uses 'mpirun -np 2' to do the simplest possible MPI run. It runs a trivially sized problem and uses a serial implementation of find. Hopefully it just works for you; if it doesn't, please let us know.

To improve it, edit i0500.sh one function at a time to grow the problem size and replace the serial find with something much faster (you may want to try the provided python parallel find). You may also need to take whatever steps necessary to run it with a job scheduler.


# Build

```
# Load MPI module
ml OpenMPI/3.1.3-GCC-8.2.0-2.31.1
./utilities/prepare.sh

```

# Run

Simply run io500 script:
```
./io500.sh
```

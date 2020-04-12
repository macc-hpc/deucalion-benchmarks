# Download NEMO and XIOS sources

### Check out NEMO sources

```
svn co http://forge.ipsl.jussieu.fr/nemo/svn/NEMO/releases/release-3.6 nemo-3.6.new
```


### Check out XIOS2.5 sources

https://nemo-related.readthedocs.io/en/latest/compilation_notes/nemo37.html  
https://nemo-related.readthedocs.io/en/latest/compilation_notes/nemo40.html

```
svn co http://forge.ipsl.jussieu.fr/ioserver/svn/XIOS/branchs/xios-2.5@1566 xios-2.5
```

# Load Necessary Modules
```
ml gcc/7.4.0 openmpi/3.1.5 hdf5/1.10.6 curl/7.68.0 boost/1.67.0 netcdf-fortran/4.5.2 netcdf-c/4.7.3
```

## Building XIOS

This repository contains custom build configuration in the `xios` folder. It may be used as reference:

```
cp xios/arch-GCC_BOB.env xios-2.5/arch
cp xios/arch-GCC_BOB.path xios-2.5/arch
cp xios/arch-GCC_BOB.fcm xios-2.5/arch
```

XIOS provided blitz library in the externs folder was not compiling correctly. It may be replaced with the latest versions:

```
cd xios-2.5/extern
wget https://github.com/blitzpp/blitz/archive/1.0.2.tar.gz
mv blitz blitz.old
tar -zxvf 1.0.2.tar.gz
mv blitz-1.0.2 blitz
cp -R blitz.old/include/blitz/gnu blitz/blitz/
```

Adjust XIOS compile script for the number os desired worker cores and respective arch configured in previous steps:

```
cd ..
./make_xios --job 12 --arch GCC_BOB
```

## Building NEMO 

This repository contains custom build configuration in the `nemo` folder. It may be used as reference:

```
cp nemo/arch-GCC_BOB.fcm -> nemo-3.6/NEMOGCM/ARCH/
```

Adjust NEMO cmopile script for the number os desired worker cores and respective arch configured in previous steps:

```
cd nemo-3.6/NEMOGCM/CONFIG
./makenemo -j 12 -m GCC_BOB -r GYRE_XIOS -n MY_GYRE add_key “key_nosignedzero"
```

## Run NEMO

Note `mesh_mask.nc` needs to be deleted before each run

```
cd MY_GYRE/EXP00
mpiexec -n 4 ../BLD/bin/nemo.exe : -n 2 ../../../../../xios-2.5/bin/xios_server.exe
```

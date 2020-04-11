# Download NEMO and XIOS sources


### Check out NEMO sources


```
svn --username USERNAME --password PASSWORD --no-auth-cache co http://forge.ipsl.jussieu.fr/nemo/svn/branches/2015/nemo_v3_6_STABLE/NEMOGCM
...
Checked out revision 6542.
```


### Check out XIOS2 sources

http://www.nemo-ocean.eu/Using-NEMO/User-Guides/Basics/XIOS-IO-server-installation-and-use

```
svn co -r819 http://forge.ipsl.jussieu.fr/ioserver/svn/XIOS/trunk xios-2.0
```



```
# Load necessary modules
ml gcc/7.4.0 openmpi/3.1.5 hdf5/1.10.6 curl/7.68.0 boost/1.67.0 netcdf-fortran/4.5.2 netcdf-c/4.7.3
```

## Building XIOS
```
arch-GCC_BOB.env -> xios-2.5/arch
arch-GCC_BOB.path -> xios-2.5/arch
arch-GCC_BOB.fcm -> xios-2.5/arch

cd xios-2.5/extern
wget https://github.com/blitzpp/blitz/archive/1.0.2.tar.gz
mv blitz blitz.old
tar -zxvf 1.0.2.tar.gz
mv blitz-1.0.2 blitz
cp -R blitz.old/include/blitz/gnu blitz/blitz/

cd ..
./make_xios --job 12 --arch GCC_BOB
```


## Building NEMO 

```
arch-GCC_BOB.fcm -> nemo-3.6/NEMOGCM/ARCH/


cd NEMOGCM/CONFIG/
./makenemo -j 12 -m GCC_BOB -r GYRE_XIOS -n MY_GYRE add_key “key_nosignedzero"
```

## Run NEMO
```
cd MY_GYRE/EXP00
mpiexec -n 4 ../BLD/bin/nemo.exe : -n 2 ../../../../../xios-2.5/bin/xios_server.exe
rm mesh_mask.nc // needs to be deleted after each run
```

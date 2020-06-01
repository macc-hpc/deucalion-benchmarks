# Download NEMO and XIOS sources

### Check out NEMO sources

```
svn co http://forge.ipsl.jussieu.fr/nemo/svn/NEMO/releases/release-3.6 nemo-3.6
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

Adjust XIOS compile script for the number of desired worker cores and respective arch configured in previous steps:

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

Note `mesh_mask.nc` needs to be deleted before each run. This prepares the input files and dictates the execution of the application as a benchmark.
```
cd MY_GYRE/EXP00
sed -i '/using_server/s/false/true/' iodef.xml
sed -i '/&nameos/a ln_useCT = .false.' namelist_cfg
sed -i '/&namctl/a nn_bench = 1' namelist_cfg
sed -i '/&namctl/a ln_ctl = .false.' namelist_cfg
sed -i '/$namrun/a nn_timing = 0' namelist_cfg 
```

Running the application
```
mpiexec -n 4 ../BLD/bin/nemo.exe : -n 2 ../../../../../xios-2.5/bin/xios_server.exe
```


## Modifying the configuration

The parameter `jp_cfg` controls the resolution.

```
rm -f time.step solver.stat output.namelist.dyn ocean.output  slurm-*  GYRE_* mesh_mask_00*
jp_cfg=4
sed -i -r \
    -e 's/^( *nn_itend *=).*/\1 21600/' \
    -e 's/^( *nn_stock *=).*/\1 21600/' \
    -e 's/^( *nn_write *=).*/\1 1000/' \
    -e 's/^( *jp_cfg *=).*/\1 '"$jp_cfg"'/' \
    -e 's/^( *jpidta *=).*/\1 '"$(( 30 * jp_cfg +2))"'/' \
    -e 's/^( *jpjdta *=).*/\1 '"$(( 20 * jp_cfg +2))"'/' \
    -e 's/^( *jpiglo *=).*/\1 '"$(( 30 * jp_cfg +2))"'/' \
    -e 's/^( *jpjglo *=).*/\1 '"$(( 20 * jp_cfg +2))"'/' \
    namelist_cfg
```

# e3sm-singularity
Singularity container for E3SM

## Install singularity

```
go get -d github.com/sylabs/singularity
export VERSION=v3.0.3
./mconfig && make -C ./builddir && sudo make -C ./builddir install
. /usr/local/etc/bash_completion.d/singularity
singularity --version
```

## Build singularity container
Download container definition file 
```
git clone https://github.com/lukaszlacinski/e3sm-singularity.git
cd e3sm-singularity
```
Build the E3SM container
```
sudo singularity build e3sm.sif e3sm.def
```

## Run E3SM developer tests
Download E3SM source (You might also want to run "git submodule update --init" after you clone the source)
```
git clone  --recursive git@github.com:E3SM-Project/E3SM.git
```
and modify cime/config/e3sm/machines/config_machines.xml, so the hostname of "linux-generic" machine matches your local machine. Define also environment variables:
```
    <environment_variables>
      <env name="NETCDF_C_PATH">$ENV{NETCDF_C_PATH}</env>
      <env name="NETCDF_FORTRAN_PATH">$ENV{NETCDF_FORTRAN_PATH}</env>
      <env name="E3SM_SRCROOT">$SRCROOT</env>
    </environment_variables>
```
Download input data from ```https://web.lcrc.anl.gov/public/e3sm/inputdata/share``` to
```${HOME}/projects/acme/cesm-inputdata/```.
And run the container
```
singularity shell --writable e3sm.sif 
export NETCDF_C_PATH=/usr/local/packages/netcdf-c-4.6.2
export NETCDF_FORTRAN_PATH=/usr/local/packages/netcdf-fortran-4.4.4
cd <E3SM_SRC_DIR>/cime/scripts
./create_test e3sm_developer
```

## Reference

* https://e3sm.org/model/running-e3sm/
* https://e3sm.org/model/running-e3sm/e3sm-quick-start/
* https://github.com/E3SM-Project/E3SM
* https://github.com/E3SM-Project/scmlib

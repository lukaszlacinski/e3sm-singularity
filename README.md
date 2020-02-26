# e3sm-singularity
Singularity container for E3SM

## Install Singularity
Install Singularity as described at [Singularity Installation](https://sylabs.io/guides/3.0/user-guide/installation.html).

## Build E3SM Singularity container
Download the container definition file 
```
git clone https://github.com/lukaszlacinski/e3sm-singularity.git
cd e3sm-singularity
```
Build the E3SM container
```
sudo singularity build e3sm.sif e3sm.def
```

## Run E3SM developer tests
Download E3SM source
```
git clone -b maint-1.0 --recursive git@github.com:E3SM-Project/E3SM.git
```
Modify `cime/config/e3sm/machines/config_machines.xml`, so the hostname of `linux-generic` machine matches your local machine hostname.
Remove `openmpi` from `MPILIBS`:
```
    <MPILIBS>mpich</mpilibs>
```
Specify `GMAKE` command above the `GMAKE_J` element:
```
    <GMAKE>make</GMAKE>
    <GMAKE_J>4</GMAKE_J>
```
Define also environment variables:
```
    <environment_variables>
      <env name="E3SM_SRCROOT">$SRCROOT</env>
    </environment_variables>
    <environment_variables mpilib="mpi-serial">
      <env name="NETCDF_PATH">/usr/local/packages/netcdf-serial</env>
      <env name="PATH">/usr/local/packages/hdf5-1.10.6-serial/bin:/usr/local/packages/netcdf-serial/bin:$ENV{PATH}</env>
      <env name="LD_LIBRARY_PATH">/usr/local/packages/szip-2.1.1/lib:/usr/local/packages/hdf5-1.10.6-serial/lib:/usr/local/packages/netcdf-serial/lib</env>
    </environment_variables>
    <environment_variables mpilib="!mpi-serial">
      <env name="NETCDF_PATH">/usr/local/packages/netcdf-parallel</env>
      <env name="PNETCDF_PATH">/usr/local/packages/pnetcdf-1.12.1</env>
      <env name="PATH">/usr/local/packages/mpich-3.3.2/bin:/usr/local/packages/hdf5-1.10.6-parallel/bin:/usr/local/packages/netcdf-parallel/bin:/usr/local/packages/pnetcdf-1.12.1/bin:$ENV{PATH}</env>
      <env name="LD_LIBRARY_PATH">/usr/local/packages/mpich-3.3.2/lib:/usr/local/packages/szip-2.1.1/lib:/usr/local/packages/hdf5-1.10.6-parallel/lib:/usr/local/packages/netcdf-parallel/lib:/usr/local/packages/pnetcdf-1.12.1/lib</env>
    </environment_variables>
```
The entire machine element will look like: <details><summary>click to expand</summary>
<p>

```
<machine MACH="linux-generic">
    <DESC>Linux workstation or laptop</DESC>
    <NODENAME_REGEX>your_hostname</NODENAME_REGEX>
    <OS>LINUX</OS>
    <TESTS>e3sm_developer</TESTS>
    <BATCH_SYSTEM>none</BATCH_SYSTEM>
    <COMPILERS>gnu</COMPILERS>
    <MPILIBS>mpich</MPILIBS>
    <RUNDIR>$ENV{HOME}/projects/acme/scratch/$CASE/run</RUNDIR>
    <EXEROOT>$ENV{HOME}/projects/acme/scratch/$CASE/bld</EXEROOT>
    <DIN_LOC_ROOT>$ENV{HOME}/projects/acme/cesm-inputdata</DIN_LOC_ROOT>
    <DIN_LOC_ROOT_CLMFORC>$ENV{HOME}/projects/acme/ptclm-data</DIN_LOC_ROOT_CLMFORC>
    <DOUT_S_ROOT>$ENV{HOME}/projects/acme/scratch/archive/$CASE</DOUT_S_ROOT>
    <DOUT_L_MSROOT>csm/$CASE</DOUT_L_MSROOT>
    <CIME_OUTPUT_ROOT>$ENV{HOME}/projects/acme/scratch</CIME_OUTPUT_ROOT>
    <BASELINE_ROOT>$ENV{HOME}/projects/acme/baselines/$COMPILER</BASELINE_ROOT>
    <CCSM_CPRNC>$CCSMROOT/tools/cprnc/build/cprnc</CCSM_CPRNC>
    <SUPPORTED_BY>jayesh at mcs dot anl dot gov</SUPPORTED_BY>
    <GMAKE>make</GMAKE>
    <GMAKE_J>4</GMAKE_J>
    <MAX_TASKS_PER_NODE>4</MAX_TASKS_PER_NODE>
    <MAX_MPITASKS_PER_NODE>2</MAX_MPITASKS_PER_NODE>
    <mpirun mpilib="default">
      <executable>mpirun</executable>
      <arguments>
        <arg name="num_tasks"> -np $TOTALPES</arg>
      </arguments>
    </mpirun>
    <module_system type="none"/>
    <environment_variables>
      <env name="E3SM_SRCROOT">$SRCROOT</env>
    </environment_variables>
    <environment_variables mpilib="mpi-serial">
      <env name="NETCDF_PATH">/usr/local/packages/netcdf-serial</env>
      <env name="PATH">/usr/local/packages/hdf5-1.10.6-serial/bin:/usr/local/packages/netcdf-serial/bin:$ENV{PATH}</env>
      <env name="LD_LIBRARY_PATH">/usr/local/packages/szip-2.1.1/lib:/usr/local/packages/hdf5-1.10.6-serial/lib:/usr/local/packages/netcdf-serial/lib</env>
    </environment_variables>
    <environment_variables mpilib="!mpi-serial">
      <env name="NETCDF_PATH">/usr/local/packages/netcdf-parallel</env>
      <env name="PNETCDF_PATH">/usr/local/packages/pnetcdf-1.12.1</env>
      <env name="PATH">/usr/local/packages/mpich-3.3.2/bin:/usr/local/packages/hdf5-1.10.6-parallel/bin:/usr/local/packages/netcdf-parallel/bin:/usr/local/packages/pnetcdf-1.12.1/bin:$ENV{PATH}</env>
      <env name="LD_LIBRARY_PATH">/usr/local/packages/mpich-3.3.2/lib:/usr/local/packages/szip-2.1.1/lib:/usr/local/packages/hdf5-1.10.6-parallel/lib:/usr/local/packages/netcdf-parallel/lib:/usr/local/packages/pnetcdf-1.12.1/lib</env>
    </environment_variables>
</machine>
```
</p>
</details>

Then add options `-lblas -llapack` to a linker in `cime/config/e3sm/machines/config_compilers.xml`:
```
<compiler COMPILER="gnu" MACH="linux-generic">
  <NETCDF_PATH> $(NETCDF_PATH)</NETCDF_PATH>
  <PNETCDF_PATH> $(PNETCDF_PATH)</PNETCDF_PATH>
  <ADD_SLIBS> $(shell $(NETCDF_PATH)/bin/nf-config --flibs) -lblas -llapack</ADD_SLIBS>
</compiler>
```
At this point you can run the container
```
mkdir $HOME/projects
singularity shell -B $HOME/projects e3sm.sif
Singularity> cd <E3SM_SRC_DIR>/cime/scripts
Singularity> ./create_test e3sm_developer
```
The developer test creates and runs about 32 cases. To create a new case separately and run it, for example water cycle 1850 with ultra low resolution:
```
Singularity> ./create_newcase --case master.A_WCYCL1850.ne4_oQU240.baseline —-compset A_WCYCL1850 —-res ne4_oQU240
Singularity> cd master.A_WCYCL1850.ne4_oQU240.baseline
Singularity> ./case.setup
Singularity> ./case.build
Singularity> ./case.submit
```
For more details on how to create a new case and run it, please refer to [E3SM Quick Start](https://e3sm.org/model/running-e3sm/e3sm-quick-start/).

## Reference

* https://sylabs.io/guides/3.0/user-guide/
* https://e3sm.org/model/running-e3sm/
* https://e3sm.org/model/running-e3sm/e3sm-quick-start/
* https://github.com/E3SM-Project/E3SM
* https://github.com/E3SM-Project/scmlib

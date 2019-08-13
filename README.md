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
singularity shell --writable e3sm.sif 
git clone  --recursive git@github.com:E3SM-Project/E3SM.git
cd <E3SM_SRC_DIR>/cime/scripts
./create_test cime_developer
./create_test e3sm_developer
```

## Reference

* https://e3sm.org/model/running-e3sm/
* https://e3sm.org/model/running-e3sm/e3sm-quick-start/
* https://github.com/E3SM-Project/E3SM
* https://github.com/E3SM-Project/scmlib

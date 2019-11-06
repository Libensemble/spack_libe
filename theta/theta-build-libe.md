## Theta build of libEnsemble

Spack: Develop branch at commit:

    commit a63e64f1c4f2f96d68daa1916ca77d4986b60cf1
    Author: Satish Balay <balay@mcs.anl.gov>
    Date:   Mon Oct 14 15:27:28 2019 -0500

Compilers:
* intel@18.0.0

YAML files:
* packages.yaml
* config.yaml (prevent trying pull again from repo if building develop)
* modules.yaml (Auto-load modules - may work without this is "spack load -r")

### Overview

A working method is to fetch packages on the front-end nodes. The to install any packages possible on the front-end, while installing other packages as necessary on the back-end.

Note: I have cobbled together a working build like this, but not yet repeated from scratch.

Python, NumPy and setuptools were all built from an existing module (see packages.py). This prevents an issue where petsc4py cannot find numpy (dual numpy builds).


### Fetching packages

Fetch from login nodes:

    spack fetch --dependencies py-libensemble @0.5.2 +mpi +scipy +petsc4py +nlopt


### Limitations and issues

* PETSc could not build with hdf5 support for parallel knl petsc build (build option 1)
* PETSc had to be compiled with the line `options.append('--with-batch=1')` added to package.py to prevent mpiexec testing
* Environment setup tries to load modules from the wrong place (reported to Spack).
* For the serial PETSc build (build 2 below), the py-petsc4py package.py had to remove petsc+mpi dependency (replace with just petsc dependency).
* For the serial PETSc build (build 2 below), not all tests run on MOM node. Some SciPy sub-packages fail.


### Installing with Spack

Two builds:

    1. Building with PETSc/NLopt/extensions to run on compute nodes (libE to be run with Balsam)
    2. Building with PETSc/NLopt/extensions to run on MOM nodes. Some issues.


#### 1. Building with PETSc/NLopt/extensions to run on compute nodes

This is for running libEnsemble on compute nodes. Currently that requires using Balsam if you
are launching sub-jobs from workers.

Limitations specific to this build:
PETSc could not build with hdf5 support

Currently Loaded Modulefiles:
  1) modules/3.2.11.3                                 13) dvs/2.7_2.2.120-6.0.7.1_12.5__g74cb2cc4
  2) intel/18.0.0.128                                 14) alps/6.6.43-6.0.7.1_5.51__ga796da32.ari
  3) craype-network-aries                             15) rca/2.2.18-6.0.7.1_5.52__g2aa4f39.ari
  4) craype/2.6.1                                     16) atp/2.1.3
  5) cray-libsci/19.06.1                              17) perftools-base/7.1.1
  6) udreg/2.3.2-6.0.7.1_5.15__g5196236.ari           18) PrgEnv-intel/6.0.5
  7) ugni/6.0.14.0-6.0.7.1_3.15__gea11d3d.ari         19) craype-mic-knl
  8) pmi/5.0.14                                       20) cray-mpich/7.7.10
  9) dmapp/7.1.1-6.0.7.1_6.6__g45d1b37.ari            21) nompirun/nompirun
 10) gni-headers/5.0.12.0-6.0.7.1_3.13__g3b1768f.ari  22) darshan/3.1.5
 11) xpmem/2.2.15-6.0.7.1_5.13__g7549d06.ari          23) trackdeps
 12) job/2.2.3-6.0.7.1_5.48__g6c4e934.ari             24) xalt


Front-end builds (inc. petsc) with:

    spack install  py-libensemble @0.5.2 +mpi +scipy +petsc4py +nlopt ^petsc~hdf5

Back-end builds (inc. hypre):

    aprun -cc none -n 1 python /home/shudson/spackdev/bin/spack install --dont-restage -j 16 py-libensemble @0.5.2 +mpi +scipy +petsc4py +nlopt ^petsc~hdf5

Once built, modules can be loaded as follows (to run libEnsemble).

    . ${SPACK_ROOT}/share/spack/setup-env.sh

    # This is fix as setup-env.sh sets module path to cray-cnl6-x86-64 instead of cray-cnl6-mic_knl
    export MODULEPATH=$SPACK_ROOT/share/spack/modules/cray-cnl6-mic_knl:$MODULEPATH

    # Should auto-load dependent modules - if not try "-r" or see modules.yaml file.
    spack load py-libensemble


#### 2. Building with PETSc/NLopt/extensions to run on MOM nodes (serial PETSc)

This version is built with serial PETSc and all extensions and compiled packages suitable to run
on the MOM nodes (Broadwell). PETSc is built also without hdf5, superlu or hypre. It is built with
the intention of using the TAO optimizers.

In preparation:

    Unload darshan (or it adds some KNL stuff)
    Unload xalt

Re-build pretty much everything with -xAVX2 (Note: -xAVX2 will be appended on the end of
compile flags, overriding the KNL flag -xMIC-AVX512)

Limitations specific to this build:
* In py-petsc4py package.py. remove petsc+mpi dependency (replace with just petsc dependency).
* Not all tests work - Some SciPy sub-packages fail on MOM nodes.

Note: In the case of ^petsc cflags="-xAVX2" the cflags applies to ^petsc even though a space (not to all packages). So must be applied separately to different packages.

Build on front-end or MOM nodes with:
    nohup spack install -j16 py-libensemble @0.5.2 +mpi +scipy +petsc4py +nlopt ^py-scipy cflags="-xAVX2" ^petsc~mpi~hdf5~hypre~superlu-dist cflags="-xAVX2" ^nlopt cflags="-xAVX2"&



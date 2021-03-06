## Summit build of libEnsemble

Spack: Develop branch at commit:

    commit 1114ae93752aa9d33c3520f72aa701d8105c09dd
    Author: Hadrien G <grasland@lal.in2p3.fr>
    Date:   Mon Nov 4 18:43:45 2019 +0100

Compilers:
* gcc/9.1.0 (latest gcc available).

YAML files:
* packages.yaml


Load these modules:

    module load gcc/9.1.0   # This ones needed to set the spec !!!!

Build with all optional dependencies on login node:

    nohup python $SPACK_ROOT/bin/spack install -j 16 py-libensemble @0.5.2 +mpi +scipy +petsc4py +nlopt&

Once built load modules with:

    . $SPACK_ROOT/share/spack/setup-env.sh
    spack load -r py-libensemble

This will autoload dependent modules as required (Python cannot use RPATHS).

However, for nlopt still need to find `nlopt.py` file (even though nlopt module is loaded):

    NLOPT_DIR=nlopt-2.5.0-oaelajewbink4t75xo55632xvt5u6khu # This will be build specific
    export PYTHONPATH=$SPACK_ROOT/opt/spack/linux-rhel7-power9le/gcc-9.1.0/$NLOPT_DIR/lib/python3.6/site-packages:$PYTHONPATH

### Actions

* nlopt does not add nlopt.py directory to PYTHONPATH. [Pull request #13688 opened 2019-11-11]

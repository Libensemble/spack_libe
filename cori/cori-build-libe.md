## Cori build of libEnsemble

Spack: balay/xsdk-0.5.0 branch at commit:

    commit d79fa0e5f04a6f516490c44ea04cc7955c64b05d
    Author: Satish Balay <balay@mcs.anl.gov>
    Date:   Thu Nov 7 17:44:43 2019 -0600


Compilers:
* intel@19.0.3.199 (default at time)

Archs:
* cray-cnl7-haswell (KNL also available)

YAML files:
* packages.yaml


Load these modules:

    module load python/3.7-anaconda-2019.07 # Get a decent python
    # Check that compiler module matches compiler above.    

Build with all optional dependencies on login node (petsc+batch avoids mpiexec testing):

    nohup python $SPACK_ROOT/bin/spack install -j 16 py-libensemble @0.5.2 +mpi +scipy +petsc4py +nlopt ^petsc+batch&

Once built load modules with:

    . $SPACK_ROOT/share/spack/setup-env.sh
    spack load -r py-libensemble

This will autoload dependent modules as required (Python cannot use RPATHS).

However, for nlopt still need to find `nlopt.py` file (even though nlopt module is loaded):

    NLOPT_DIR=nlopt-2.5.0-ohcxbj4lpderbwzvd3wxf6qpmnlgagcy # This will be build specific
    export PYTHONPATH=$SPACK_ROOT/opt/spack/cray-cnl7-haswell/intel-19.0.3.199/$NLOPT_DIR/lib/python3.6/site-packages:$PYTHONPATH

### Actions

* nlopt does not add nlopt.py directory to PYTHONPATH. [Pull request #13688 opened 2019-11-11]

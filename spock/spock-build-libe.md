## Spock build of libEnsemble

Spack: Develop branch at commit:

commit 1eec99ce1d0ab8ed27060ace3daa2c1de3f80317 (HEAD -> develop, origin/develop, origin/HEAD)
Author: Manuela Kuhn <36827019+manuelakuhn@users.noreply.github.com>
Date:   Sat Dec 4 05:05:05 2021 +0100

    py-imagesize: add 1.3.0 (#27787)

Compilers:

    Cray programming environment loaded
    shudson@login2:spack$ which cc
    /opt/cray/pe/craype/2.7.11/bin/cc

Architecture found:

    arch=cray-sles15-zen2

YAML files:
* packages.yaml

Load these modules:

    module load cray-python

The module must be loaded so that /opt/cray/pe/gcc-libs is added to PATH (even if packages.yaml points to python/numpy).

First I did simple build with minimal dependencies:

    spack install py-libensemble

Once built load modules with:

    . $SPACK_ROOT/share/spack/setup-env.sh
    spack load py-libensemble  # -r no longer works. Check added install dir to PYTHONPATH

Check getting Spack build libEnsemble:

shudson@login2:spack$ python
    Python 3.9.4 (default, Aug 16 2021, 21:41:42)
    [GCC 9.3.0 20200312 (Cray Inc.)] on linux
    Type "help", "copyright", "credits" or "license" for more information.
    >>> import libensemble
    >>> libensemble.__path__
    ['/autofs/nccs-svm1_home1/shudson/spock/spack/opt/spack/cray-sles15-zen2/gcc-11.2.0/py-libensemble-0.8.0-bjk36afkm6jiadncyvwzeqc2evxf2bso/lib/python3.9/site-packages/libensemble']

Run smoke test:

    spack load -r py-libensemble

    ==> Spack test t5oe2bk3bvjh237gz5y2i3prm42tn6ed
    ==> Testing package py-libensemble-0.8.0-bjk36af


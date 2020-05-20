This repository provides a log of how I built libEnsemble with all dependencies on various platforms.

The spec for these builds is:

    py-libensemble @0.5.2 +mpi +scipy +petsc4py +nlopt

**Update**: If no mpiexec is available where building - disable PETSc tests with:

    py-libensemble @0.5.2 +mpi +scipy +petsc4py +nlopt ^petsc+batch

See platform instructions for platform specific build instructions and issues.

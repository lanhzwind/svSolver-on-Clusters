# svSolver-on-Computer-Clusters

---

## Compiling

To compile SimVascular flowsolver (svSolver) only on clusters, you need to:
- make sure mpi and compiler is available. Normally you can use the command **module load** to load mpi and compiler. They also need to be compatible, which means if you choose gcc (or intel) as the compiler, the mpi should also be compiled by gcc (or intel). 

- make corresponding changes in the following three files. Please create one if one of them doesn't exist.

**BuildWithMake/cluster_overrides.mk**
~~~
CLUSTER = x64_linux
# COMPILER_VERSION = { intel, gnu }
COMPILER_VERSION = intel
~~~

**BuildWithMake/site_overrides.mk**
~~~
# Build only the 3D Solver
EXCLUDE_ALL_BUT_THREEDSOLVER = 1
# Don't use VTK
FLOWSOLVER_VERSION_USE_VTK_ACTIVATE = 0
# Don't use default settings for mpi. Speficy mpi on compiler.xxx.mk.
MAKE_WITH_MPICH2 = 0
~~~

**BuildWithMake/MakeHelpers/compiler.intel.x64_linux.mk** if using intel compiler
~~~
CXX             = mpicxx -pthread
CC              = mpicc -pthread
F90             = mpif90 -threads -fpp
CXXDEP          = mpicxx -MM
CCDEP           = mpicc -MM
...
...


~~~

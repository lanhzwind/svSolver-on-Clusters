# General Instructions 
Here are general instrcutions about how to compile SimVascular flowsolver (svSolver) on linux clusters. Some examples for some linux clusters we are using now are shown in separate files. They show how to compile and run svSolver on those clusters. 

(1) Download the latest SimVascular code from github.com/SimVascular/SimVascular.

(2) To compile only SimVascular flowsolver (svSolver) on linux clusters, you need to:
- make sure mpi and compiler is available. Normally you can use the command **module load** to load mpi and compiler. They also need to be compatible, which means if you choose gcc (or intel) as the compiler, the mpi should also be compiled by gcc (or intel). 

- make corresponding changes in the following files. Please create one if one of them doesn't exist.

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
...
#mpi wrappers are recommended for compilers.
CXX             = mpicxx -pthread
CC              = mpicc -pthread
F90             = mpif90 -threads -fpp
CXXDEP          = mpicxx -MM
CCDEP           = mpicc -MM
...
...
#MPI settings at the end
#openmpi/1.8.7
MPI_LIBS     = -lmpi_usempif08 -lmpi_usempi_ignore_tkr -lmpi_mpifh -lmpi
#mpich
#MPI_LIBS    = -lmpichf90 -lmpich -lopa -lmpl -lrt -lpthread
~~~

**BuildWithMake/MakeHelpers/compiler.gnu.x64_linux.mk** if using gnu compiler
~~~
...
#mpi wrappers are recommended for compilers.
CXX             = mpicxx -pthread -w
CC              = mpicc -pthread -w
F90             = mpif90 -cpp
CXXDEP          = mpicxx -MM
CCDEP           = mpicc -MM
...
...
#MPI settings at the end
#Same as the above.
~~~

MPI_LIBS is for choosing fortran libraries. For openmpi, different versions may have different settings. You need to use **mpif90 --showme:link** to check after you load openmpi.

When using mpi wrappers, you don't need to explicitly set mpi include dir and compiling/linking flags. But if you can't use module load or mpi wrappers have some issues, use compiler commands instead of mpi wrappers and specify mpi as below:
~~~
MPI_TOP     =  [mpi path]
MPI_INCDIR  = -I $(MPI_TOP)/include -L $(MPI_TOP)/lib
MPI_LIBS    = [shown as the above]
MPI_SO_PATH = $(MPI_TOP)/lib
MPIEXEC_PATH  = [mpi exec path]
MPIEXEC       = mpiexec
~~~

Normally intel libs from sv_extern are not needed when using gnu compiler. But in case you need them, set up in this file.
**BuildWithMake/pkg_overrides.mk**
~~~
INTEL_COMPILER_SO_PATH  = [intel libs path]
CC_LIBS   = -L$(INTEL_COMPILER_SO_PATH) -lirc -limf -lsvml -ldl
CXX_LIBS  = -L$(INTEL_COMPILER_SO_PATH) -lirc -limf -lsvml -ldl
F90_LIBS  = -L$(INTEL_COMPILER_SO_PATH) -lirc -limf -lsvml -ldl -lifcore -lifport -lgfortran -lm
~~~

(3) After all the settings are completed, go the directory BuildWithMake and**make**! The compiled svSolver will be  BuildWithMake/Bin/flowsolver.exe.

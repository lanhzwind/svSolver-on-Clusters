# General Instructions 
Here are general instrcutions about how to compile SimVascular flowsolver (svSolver) on linux clusters. Some examples for some linux clusters we are using now are shown in separate files. They show how to compile and run svSolver on those clusters. Download the latest SimVascular code from github.com/SimVascular/SimVascular.

##Compiling
###Step 1
Make sure mpi and compiler is available. Normally you can use the command **module load** to load mpi and compiler. They also need to be compatible, which means if you choose gcc (or intel) as the compiler, the mpi should also be compiled by gcc (or intel). 

###Step 2
Make corresponding changes in the following files. Please create one if one of them doesn't exist. There are two methods to do it, the first one is easy, and the the second gives more control.

**Method 1**

This method only changes two files and works at most time. In case it doesn't because mpi wrappers have issues or the clusters don't have normal wrappers, you need to use Method 2.

**BuildWithMake/cluster_overrides.mk**
~~~
CLUSTER = x64_linux
# CXX_COMPILER_VERSION = { icpc, gcc }
CXX_COMPILER_VERSION = icpc
# FORTRAN_COMPILER_VERSION = { ifort, gfortran }
FORTRAN_COMPILER_VERSION = ifort
~~~

**BuildWithMake/site_overrides.mk** (or global_overrides.mk, either is ok)
~~~
# Build only the 3D Solver
EXCLUDE_ALL_BUT_THREEDSOLVER = 1
# Don't use VTK
FLOWSOLVER_VERSION_USE_VTK_ACTIVATE = 0
# Use defautl settings for mpi
MAKE_WITH_MPI = 1
# Choose openmpi
MAKE_WITH_OPENMPI = 1
MAKE_WITH_MPICH = 0
~~~

**Method 2**

This method changes several files and gives more control about the compiling process. 

**BuildWithMake/cluster_overrides.mk**
~~~
CLUSTER = x64_linux
# CXX_COMPILER_VERSION = { icpc, gcc }
CXX_COMPILER_VERSION = icpc
# FORTRAN_COMPILER_VERSION = { ifort, gfortran }
FORTRAN_COMPILER_VERSION = ifort
~~~

**BuildWithMake/site_overrides.mk**
~~~
# Build only the 3D Solver
EXCLUDE_ALL_BUT_THREEDSOLVER = 1
# Don't use VTK
FLOWSOLVER_VERSION_USE_VTK_ACTIVATE = 0
# Don't use defautl settings for mpi
MAKE_WITH_MPI = 0
# Give a name for the mpi you use
MPI_NAME=openmpi

~~~

**BuildWithMake/pkg_overrides.mk** if using icpc
~~~
...
#mpi wrappers are recommended for compilers.
#if using icpc and ifort
CXX             = mpicxx -pthread
CC              = mpicc -pthread
CXXDEP          = mpicxx -MM
CCDEP           = mpicc -MM
F90             = mpif90 -threads -fpp
##if using gcc and gfortran
#CXX             = mpicxx -pthread -w
#CC              = mpicc -pthread -w
#CXXDEP          = mpicxx -MM
#CCDEP           = mpicc -MM
#F90             = mpif90 -cpp
...
...
#MPI settings at the end
#openmpi/1.8.7
MPI_LIBS     = -lmpi_usempif08 -lmpi_usempi_ignore_tkr -lmpi_mpifh -lmpi
#mpich
#MPI_LIBS    = -lmpichf90 -lmpich -lopa -lmpl -lrt -lpthread
~~~

MPI_LIBS is for choosing fortran libraries. Different mpis or even different verions may have different settings. For openmpi, you need to use **mpif90 --showme:link** to check after you load openmpi. For mpich, you can use **mpif90 -link_info**.

When using mpi wrappers, you don't need to explicitly set mpi include dir and compiling/linking flags. But if you can't use module load or mpi wrappers have some issues, use compiler commands instead of mpi wrappers and specify mpi as below:
~~~
MPI_TOP     =  [mpi path]
MPI_INCDIR  = -I $(MPI_TOP)/include -L $(MPI_TOP)/lib
MPI_LIBS    = [shown as the above]
MPI_SO_PATH = $(MPI_TOP)/lib
MPIEXEC_PATH  = [mpi exec path]
MPIEXEC       = mpiexec
~~~

Normally intel libs from sv_extern are not needed when using gnu compiler. But in case you need them, set up in at the end of pkg_overrides.mk.
~~~
INTEL_COMPILER_SO_PATH  = [intel libs path]
CC_LIBS   = -L$(INTEL_COMPILER_SO_PATH) -lirc -limf -lsvml -ldl
CXX_LIBS  = -L$(INTEL_COMPILER_SO_PATH) -lirc -limf -lsvml -ldl
F90_LIBS  = -L$(INTEL_COMPILER_SO_PATH) -lirc -limf -lsvml -ldl -lifcore -lifport -lgfortran -lm
~~~

###Step 3

After all the settings are completed, go to the directory BuildWithMake and **make**! The compiled svSolver will be located BuildWithMake/Bin/.

#Compiling

~~~
module swap PrgEnv-cray PrgEnv-intel
~~~

**BuildWithMake/cluster_overrides.mk**
~~~
CLUSTER = x64_linux

CXX_COMPILER_VERSION = icpc
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
MPI_NAME=mpich
~~~

**BuildWithMake/pkg_overrides.mk**
~~~
CXX             = CC -pthread
CC              = cc -pthread
CXXDEP          = CC -MM
CCDEP           = cc -MM
F90             = ftn -threads -fpp

GLOBAL_FFLAGS   = $(BUILDFLAGS) $(DEBUG_FFLAGS) $(OPT_FFLAGS) -W0 -132
~~~

#Testing

~~~
qsub -I -l size=16,walltime=1:00:00
~~~

~~~
module swap PrgEnv-cray PrgEnv-intel
cd /lustre/medusa/[user name]
aprun -n 4 ~/SimVascular/BuildWithMake/Bin/flowsolver.exe
~~~

#Running jobs

example.job
~~~
#!/bin/bash 
#PBS -A [allocation name] 
#PBS -l size=32,walltime=01:30:00  

module swap PrgEnv-cray PrgEnv-intel
cd $PBS_O_WORKDIR
aprun -n 32 ~/SimVascular/BuildWithMake/Bin/flowsolver.exe
~~~

Run the job
~~~
qsub example.job
~~~

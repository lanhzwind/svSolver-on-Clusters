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

**BuildWithMake/MakeHelpers/compiler.icpc.x64_linux.mk** if using icpc
~~~
...
#mpi wrappers are recommended for compilers.
CXX             = CC -pthread
CC              = cc -pthread
CXXDEP          = CC -MM
CCDEP           = cc -MM
...
...
~~~

**BuildWithMake/MakeHelpers/compiler.ifort.x64_linux.mk** if using ifort
~~~
...
#mpi wrappers are recommended for compilers.
F90             = ftn -threads -fpp
...
...
#MPI settings at the end
#mpich
MPI_LIBS    = -lmpichf90 -lmpich -lmpl -lrt -lpthread
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

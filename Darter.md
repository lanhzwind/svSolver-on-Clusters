#Compiling

~~~
module swap PrgEnv-cray PrgEnv-intel
~~~

**BuildWithMake/cluster_overrides.mk**
~~~
CLUSTER = x64_linux
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

**BuildWithMake/MakeHelpers/compiler.intel.x64_linux.mk**
~~~
...
#Cray wrappers are used for compilers.
CXX             = CC -pthread
CC              = cc -pthread
F90             = ftn -threads -fpp
CXXDEP          = CC -MM
CCDEP           = cc -MM
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

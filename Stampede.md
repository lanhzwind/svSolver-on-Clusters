#Compiling

intel/13.0.2.146 and mvapich2/1.9a2 are loaded by default.

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
#mpi wrappers are recommended for compilers.
CXX             = mpicxx -pthread
CC              = mpicc -pthread
F90             = mpif90 -threads -fpp
CXXDEP          = mpicxx -MM
CCDEP           = mpicc -MM
...
...
#MPI settings at the end
#mpich
MPI_LIBS    = -lmpichf90 -lmpich -lopa -lmpl -lrt -lpthread
~~~

#Testing

~~~
idev
~~~

~~~
ibrun -np 2 ~/SimVascular/BuildWithMake/Bin/flowsolver.exe
~~~

#Running jobs

example.job
~~~
#!/bin/sh
#SBATCH -A [allocation name]  #account code
#SBATCH -J example            # Job name
#SBATCH -o example.%j.out   # stdout; %j expands to jobid
#SBATCH -e example.%j.err   # stderr; skip to combine stdout and stderr
#SBATCH -t 00:30:00       # max time
#SBATCH -p development    # queue
#SBATCH -n 32             # Total number of MPI tasks (if omitted, n=N)

#SBATCH --mail-user=[email address]
#SBATCH --mail-type=begin

#run script
ibrun ~/SimVascular/BuildWithMake/Bin/flowsolver.exe
~~~

Run the job
~~~
sbatch example.job
~~~

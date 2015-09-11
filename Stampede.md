#Compiling

intel/13.0.2.146 and mvapich2/1.9a2 are loaded by default.

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
# Use defautl settings for mpi
MAKE_WITH_MPI = 1
# Choose openmpi
MAKE_WITH_OPENMPI = 0
MAKE_WITH_MPICH = 1
~~~

**BuildWithMake/pkg_overrides.mk**
~~~
GLOBAL_FFLAGS   = $(BUILDFLAGS) $(DEBUG_FFLAGS) $(OPT_FFLAGS) -W0 -132
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

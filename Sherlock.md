#Compiling

##Method 1: Using Intel Compilers

~~~
module load openmpi/1.8.7/intel
~~~

**BuildWithMake/cluster_overrides.mk**
~~~
CLUSTER = x64_linux

CXX_COMPILER_VERSION = icpc
FORTRAN_COMPILER_VERSION = ifort
~~~

**BuildWithMake/site_overrides.mk**
~~~
#Don't use shared globals
MAKE_WITH_GLOBALS_SHARED = 0
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

**BuildWithMake/pkg_overrides.mk**
~~~
GLOBAL_FFLAGS   = $(BUILDFLAGS) $(DEBUG_FFLAGS) $(OPT_FFLAGS) -W0 -132
~~~

##Method 2: Using GCC Compilers with  intel linking

~~~
module load openmpi/1.8.7/gcc
module load intel/13sp1up1
~~~

**BuildWithMake/cluster_overrides.mk**
~~~
CLUSTER = x64_linux

CXX_COMPILER_VERSION = gcc
FORTRAN_COMPILER_VERSION = gfortran
~~~

**BuildWithMake/site_overrides.mk**
~~~
#Don't use shared globals
MAKE_WITH_GLOBALS_SHARED = 0
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

**BuildWithMake/pkg_overrides.mk**
~~~
GLOBAL_FFLAGS   = $(BUILDFLAGS) $(DEBUG_FFLAGS) $(OPT_FFLAGS) -ffixed-line-length-132
F90_LIBS        =  -lirc -limf -lsvml -lifcore -lifport -lgfortran -lm
~~~

#Testing
Make sure openmpi/1.8.7/intel (for Method 1) or openmpi/1.8.7/gcc and intel/13sp1up1 for (Method 2) is loaded

~~~
sdev
~~~

~~~
srun -n 1 ~/SimVascular/BuildWithMake/Bin/flowsolver.exe
~~~

#Running jobs

example.job
~~~
#!/bin/bash
#
#all commands that start with SBATCH contain commands that are just used by SLURM for scheduling
#################
#set a job name
#SBATCH --job-name=example
#################
#a file for job output, you can check job progress
#SBATCH --output=example.out
#################
# a file for errors from the job
#SBATCH --error=example.err
#################
#time you think you need; default is one hour
#in minutes in this case
#SBATCH --time=60:00
#################
#quality of service; think of it as job priority
#SBATCH --qos=normal
#################
#number of nodes you are requesting
#SBATCH --nodes=8
#################
#memory per node; default is 4000 MB per CPU
#SBATCH --mem=4000
#you could use --mem-per-cpu; they mean what we are calling cores
#################
#tasks to run per node; a "task" is usually mapped to a MPI processes.
# for local parallelism (OpenMP or threads), use "--ntasks-per-node=1 --cpus-per-tasks=16" instead
#SBATCH --ntasks-per-node=16
#################
#partition to use
#SBATCH --partition=<partition_name>
#################

#Method 1
module load openmpi/1.8.7/intel

#Method 2
#module load openmpi/1.8.7/gcc
#module load intel/13sp1up1

#run with mpirun
srun ~/SimVascular/BuildWithMake/Bin/flowsolver.exe
~~~

Run the job
~~~
sbatch example.job
#Use own partition, if the partition is not specified in example.job
sbatch -p <partition_name> example.job
#use other owner's partition, if the partition is not specified in example.job
sbatch -p owners example.job
~~~



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
#SBATCH -A [allocation name]
##################
#set a job name
#SBATCH -J example
#################
#a file for job output, you can check job progress
#SBATCH -o example.%j.out
#################
# a file for errors from the job
#SBATCH -e example.%j.err
#################
#time you think you need; default is one hour
#in minutes in this case
#SBATCH -t 00:40:00
#################
#quality of service; think of it as job priority
#SBATCH -p normal
#################
#number of total tasks you are requesting
#SBATCH -n 256
#################
#memory per node; default is 4000 MB per CPU
##SBATCH --mem=4000
#you could use --mem-per-cpu; they mean what we are calling cores
#################
#tasks to run per node; a "task" is usually mapped to a MPI processes.
# for local parallelism (OpenMP or threads), use "--ntasks-per-node=1 --cpus-per-tasks=16" instead
#################
#################

#SBATCH --mail-user=[email address]
#SBATCH --mail-type=begin

#run script
ibrun ~/SimVascular/BuildWithMake/Bin/flowsolver.exe
~~~

Run the job
~~~
sbatch example.job
~~~

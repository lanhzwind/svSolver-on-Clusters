#Compiling

~~~
module load openmpi/1.8.7/intel
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
~~~

#Testing
Make sure openmpi/1.8.7/intel is loaded

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

#load mpi
module load openmpi/1.8.7/intel

#run with mpirun
srun ~/SimVascular/BuildWithMake/Bin/flowsolver.exe
~~~

Run the job
~~~
sbatch example.job
~~~



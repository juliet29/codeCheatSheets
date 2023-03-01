# Sherlock Cheat Sheet

## Basics 
### Login 
```bash
ssh jnwagwu@login.sherlock.stanford.edu
```
### Edit a file 
```bash
nano <path/to/file>
# or can use micro... 
# TODO fix this so usable everywhere 
./micro <path/to/file>
ctrl+q to close micro 
```

### Copying files 

**copy local to sherlock**
-> this is done on a local bash terminal, btw 
``` bash
# generally 
scp cfd/<subfolder>/<file> jnwagwu@login.sherlock.stanford.edu:<location in sherlock to copy to, leave empty if home>

# or 
scp cfd/runFoamContainer.sh jnwagwu@login.sherlock.stanford.edu:
# or
scp -r cfd/of2012_projects/hr11D_sbSlipShmWindows jnwagwu@login.sherlock.stanford.edu:
```

**copy sherlock to local**
``` bash
# generally 
scp <sunetid>@login.sherlock.stanford.edu:foo local_foo

# or
scp -r jnwagwu@login.sherlock.stanford.edu:hr11DS/postProcessing/samples/130 cfd/of2012_projects/hr11D_sbSlipShmWindows/postProcessing/samples/
# or
scp -r jnwagwu@login.sherlock.stanford.edu:sing.sh _UILCode/cfd/sherlock_scripts/sing_112622.sh
# or
scp -r jnwagwu@login.sherlock.stanford.edu:hr11DS.sbatch _UILCode/cfd/sherlock_scripts/hr11DS_112622.sbatch

scp -r jnwagwu@login.sherlock.stanford.edu:pp .
```


### Finding and deleting files/directories 
(can go in general code cheat sheet)
```bash 
# first find files (in current path, can replace . with real path)
find . -name '*py*'

# then delete as needed
find . -name '*py*' -delete


# --- finding other groups of files 

# all files with numbers
find . -name "[0-9]*"

find . -name '*([0-5][0-9]|6[0-7])*'

find <path> -type f | grep -E "/myfile_([0-9]|[0-5][0-9]|6[0-7])\.log$"

# files between 1 and 59? -> cant get delete to work, but this is recursive 
find . -type d | grep -E "([1-5]|[1-5])"
find .|  grep -E "([1-5]|[1-5])"
```


-----------
## Running a program (simple)
request resources
```bash
srun -c 4 --pty bash 

# simpler 
sdev 
```

start a singularity shell 
```bash
singularity shell --pwd /Shelter_example_case openfoam_by_ansible_ubuntu1804_with_p2012_swak4foam.sif 
# another approach
singularity shell openfoam_by_ansible_ubuntu1804_with_p2012_swak4foam.sif 
```

execute a command directly 
```bash
singularity exec ./openfoam_by_ansible_ubuntu1804_with_p2012_swak4foam.sif /home/openfoam/OpenFOAM/OpenFOAM-v2012/bin/foamSystemCheck
# another approach 
cd simg
singularity exec --pwd $HOME/hr11DS  ./openfoam_by_ansible_ubuntu1804_with_p2012_swak4foam.sif /home/openfoam/OpenFOAM/OpenFOAM-v2012/bin/foamSystemCheck
```

----

## Workflow for automating an OF Case in Sherlock 

### 1. Move code, data to cluster


``` bash
# from outside the folder
scp -r cfd/of2012_projects/hr11D2 jnwagwu@login.sherlock.stanford.edu:


# from inside the folder 
scp -r . jnwagwu@login.sherlock.stanford.edu:ofCases/<desiredSherlockCaseName>
```





### 2. Get code running 

``` bash
# get into singularity terminal
cd simg
singularity shell openfoam_by_ansible_ubuntu1804_with_p2012_swak4foam.sif 

# list binaries to check all is well 
ls -lrths /home/openfoam/OpenFOAM/OpenFOAM-v2012/bin/

# run a command to check if working, again to check all is well 
/home/openfoam/OpenFOAM/OpenFOAM-v2012/bin/foamSystemCheck

# add commands to bashrc
source /home/openfoam/OpenFOAM/OpenFOAM-v2012/etc/bashrc

# go to case
cd Shelter_example_case/

# run blockMesh
blockMesh

# run the case
buoyantBoussinesqSimpleFoam > log &

# leave singularity shell 
exit 
```



### 3. run code on dev node
determine how much ram etc needed?
``` bash
# super quickly available interactive shell (4GB memory for 1 hr)
sdev 

# > repeat stage 2 but now in sdev 

# see resources being consumed, note this for making sbatch file...
top 
``` 

### 4. make and  run batch file ** (WIP)

Tried to run commands in sing.sh directly, but this did not work....

```bash
singularity run source /home/openfoam/OpenFOAM/OpenFOAM-v2012/etc/bashrc 

singularity run openfoam_by_ansible_ubuntu1804_with_p2012_swak4foam.sif  "$HOME/./sing.sh"

singularity exec ./openfoam_by_ansible_ubuntu1804_with_p2012_swak4foam.sif  "$HOME/./sing.sh"
```

Alternatively, submit a test.sbatch file that has the singularity command within it
``` bash
# command in test.sbatch 
singularity exec --pwd $HOME/hr11DS ./openfoam_by_ansible_ubuntu1804_with_p2012_swak4foam.sif  "$HOME/./sing.sh"
```

**Running the batch file** 
``` bash
# remove output files
rm *slurm*

# run the batch job 
sbatch test.sbatch

# or do all at once => rm slurm files and run the needed files 
rm *slurm* & sbatch test.sbatch
rm *slurm* & sbatch hr11DS.sbatch


# check position in queue, will also give job id/node name associated w/ slurm file
squeue -u $USER

# monitor the job  
ssh <sh02-10n48 or other node name>
top # to see the use of resources..


# if new slurm out put file 
cat slurm-….
```

----
## Installing software 

```bash
# load modules if needed 
# use git version, cmake, make etc 

```

-----
## Example Scripts

test.sbatch calls sing.sh 

### test.sbatch
File to request batch job ~ test.sbatch 
```bash
#!/bin/bash
#SBATCH --job-name=hr11DS
#SBATCH --time=10:00
#SBATCH -p normal
#SBATCH -c 1
#SBATCH --mem=5G
#SBATCH --mail-type=BEGIN,END,FAIL,TIME_LIMIT_50

# --- Commands start here ----

start=$(date)
echo $start

# go to where a singularity image can be found
cd simg

# test this out 
srun sleep 30

# run a singularity executable 
srun --time=10:00 --mem=5G  singularity exec --pwd $HOME/hr11DS ./openfoam_by_ansible_ubuntu1804_with_p2012_swak4foam.sif  "$HOME/./sing.sh"

end=$(date)
echo $end

```

### sing.sh
File to execute a singilarity command ~ sing.sh
```bash 
#!/bin/bash

# script to run when in singularity container
source /home/openfoam/OpenFOAM/OpenFOAM-v2012/etc/bashrc

ls -lrths /home/openfoam/OpenFOAM/OpenFOAM-v2012/bin/ > log1

echo "hi"

# go to case
# cd ../hr11DS

ls > log2

echo "bye"

# 
# 
# # run the case
buoyantBoussinesqSimpleFoam > log &


```



<!-- Making the sbatch file 

=> requesting resources 
￼

=> commands  -->

<!-- >>  in future, might want to do all this in $SCRATCH -->
￼
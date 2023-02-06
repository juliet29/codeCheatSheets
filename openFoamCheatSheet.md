# 👩🏾‍💻 **OpenFoam Cheat Sheet** 


# Navigating Docker / Directories 
to start OF docker container with pyfoam and swak4foam and have access to /cfd, run this command inside /cfd folder
```
./runFoamContainer.sh bgschaid/openfoam_by_ansible:ubuntu1804_with_p2012_swak4foam 
```

when start another container, need to rename the old one 
```
docker rename bgschaid_openfoam_by_ansible_ubuntu1804_with_p2012_swak4foam_run of1 
```
if get stuck in foam directory 
```
cd ../../foamdata/ 
```
see path for present/current working directory 
```
pwd
```

list all files in directory cat 
```
ls *
```


# Setting Up Cases 
copy cases over from a tutorial to the current directory 
```
cp -r $FOAM_TUTORIALS/incompressible/icoFoam/cavity examples
```

copy/clone a case to a new directory with only the essentials 
-don't have to remake mesh! 
```
pyFoamCloneCase.py hotRoom6H_yPlus/ hotRoom6I_yPlusBox
```

create a mesh 
```
blockMesh
```

convert a GMSH mesh
```
gmshToFoam <name of mesh>
```

check on a mesh 
```
checkMesh (mesh should be ok)
```

## snappy hex mesh! —————
1. chage blockMeshDict as needed, then create domain. delete the 0.1 and 0.2 folder if have run `snappyHexMesh` command.
    ```
    rm -r 0.1 0.2
    blockMesh
    ```

2. make a triSurface folder in the case/constant folder
    ```
    mkdir constant/triSurface
    ```

3. copy over the stl 
   * copy and rename one stl
        ```
        cp cad/box5H.stl of2012_projects/hr11_shm5H/constant/triSurface/box.stl
        ```
   * copy an stl without renaming 
       ```
       cp /home/wardah/temp/text_file1.txt /home/wardah/temp2
       ```
   * copy over multiple stls 
       ```
       cp -r ../cad/STLs/blenderRoom_020623/* hr12b/constant/triSurface
        ```



4. check stl position in paraview: open the stl file in paraview and decrease the opacity of the mesh, then open the stl files 
    ```
    paraview <..>.foam &
    ```

5. extract features 
    ```
    surfaceFeatureExtract
    ```

6. mesh it 
    ```
    snappyHexMesh 
    ```

## end snappy hex mesh! —————

create baffles for objects inside a mesh/ allow OF to recognize objects inside a mesh (Gmsh) 
```
createBaffles -overwrite
```

PyFoam case report 
```
pyFoamCaseReport.py . --short-bc-report --internal-field --linear-solvers --case-size --relaxation-factors --file=caseReport 
```
```
pyFoamCaseReport.py . --full-report --file=caseReport
```



# Running Applications
foamJob to run case  (don’t do this..)
```
foamJob simpleFoam 
foamJob buoyantFoam 
foamJob buoyantBoussinesqSimpleFoam
```

run and send to log file 
```
buoyantBoussinesqSimpleFoam log &
```

pyfoam to run case and automatically clear existing time directories 
```
pyFoamRunner.py --clear auto
```

pyfoam to run case and store graphs -- custom graphs using entries in controlDict, and customRegexp (see Scripts 👩🏾‍💻)
```
pyFoamPlotRunner.py --clear --progress --auto --hardcopy --prefix=r0922_1430 auto 
```

absence of clear will start running simulation from last saved time step**
```
pyFoamPlotRunner.py --progress --auto --hardcopy --prefix=r0922_1430 auto
``` 


see log in bash terminal 
``` 
tail -f log 
tail -f log.simpleFoam
tail -f PyFoamRunner.simpleFoam.logfile
```


watch residuals as application runs  (see Scripts 👩🏾‍💻) - need to run this in a regular bash terminal 
```
gnuplot Residuals - &
```

exit
print all jobs with PID 
```
ps S
```

print all jobs running in terminal 
```
ps -r
```

kill job with certain pid 
```
kill -9 <job pid number>
```

kill job with certain name in command 
```
pkill -9 -f gnuplot
```
￼

ps options 
```
ps --help simple 
```
￼



# Post-Processing (PP) 
list available function objects 
```
postProcess -list 
```

pp for function objects requiring turbulence models 
```
postProcess -func <name of object)
```


pp for function objects requiring turbulence models
``` 
simpleFoam -postProcess -func yPlus 
simpleFoam -postProcess -func CourantNo
```

pp based on self-made function objects 
```
postProcess -func singleGraph
postProcess -func <name of sampling file and disctionarygnuplot plot "postProcessing/singleGraph/0.5/line_p.xy"
```



# Paraview 
run paraview in bash shell (not on OF docker container!)
```
paraview foam.foam &
paraview <other_name>.foam &
```

# Relevant to All Steps  
for help with anything PyFoam 
```
pyFoamCaseReport.py --help
```

to view a file on the command line  
```
cat <../path/to/file >
``` 


# Breakdown of OpenFoam 2012 + Keywords 
(relevant to turbulence models)
    * Of 2012
        * Applications (solver definitions) -> can type “app”  in terminal to get here 
            * Solvers -> “solvers” / “sol”
                * Incompressible 
                * Heat transfer 
                * & more …
            * Utilities -> “utilities”
            * Tools
            * Test
        * Src -> can type “src” to get here
            * TurbulenceModels
            * atmosphericModels 
            * fvOptions 
            * finiteVolume 
            * & more …
        * lsorials -> can type “tut” to get here 
        * & more …

# Scripts 👩🏾‍💻
(Mostly for GNU plots)

## Residuals Script 👩🏾‍💻
```
set logscale y
set title "Residuals"
set ylabel 'Residual'
set xlabel 'Iteration'
plot "< cat log.simpleFoam | grep 'Solving for Ux' | cut -d' ' -f9 | tr -d ','" title 'Ux' with lines,\
"< cat log.simpleFoam | grep 'Solving for Uy' | cut -d' ' -f9 | tr -d ','" title 'Uy' with lines,\
"< cat log.simpleFoam | grep 'Solving for Uz' | cut -d' ' -f9 | tr -d ','" title 'Uz' with lines,\
"< cat log.simpleFoam | grep 'Solving for epsilon' | cut -d' ' -f9 | tr -d ','" title 'epsilon' with lines,\
"< cat log.simpleFoam | grep 'Solving for k' | cut -d' ' -f9 | tr -d ','" title 'k' with lines,\
"< cat log.simpleFoam | grep 'Solving for p' | cut -d' ' -f9 | tr -d ','" title 'p' with lines
pause 300
reread
```
**use command f to change name of the log file based on what its called in the folder!**


## CustomRegexp Script 👩🏾‍💻 
```
velocity {
theTitle "Ux";
ylabel "Ux";
expr "Expression velocity :  min=(.+) weightedQuantile0.1=(.+)weightedAverage=(.+) weightedQuantile0.9=(.+) max=(.+)"; 
titles (
min
weightedQuantile0.1
average
weightedQuantile0.9
max );
}

nut {
theTitle "nut";
ylabel "nut";
expr "Expression nut :  min=(.+) weightedQuantile0.1=(.+)weightedAverage=(.+) weightedQuantile0.9=(.+) max=(.+)"; 
titles (
min
weightedQuantile0.1
average
weightedQuantile0.9
max );
}


k {
theTitle "k";
ylabel "k";
expr "Expression omega :  min=(.+) weightedQuantile0.1=(.+)weightedAverage=(.+) weightedQuantile0.9=(.+) max=(.+)"; 
titles (
min
weightedQuantile0.1
average
weightedQuantile0.9
max );
}
```




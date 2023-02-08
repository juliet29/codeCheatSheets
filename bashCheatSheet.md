# Bash Cheat Sheet 

## Copy files
``` bash 
# copy and rename one stl
cp cad/box5H.stl of2012_projects/hr11_shm5H/constant/triSurface/box.stl

# copy an stl without renaming 
cp /home/wardah/temp/text_file1.txt /home/wardah/temp2

# copy over multiple stls 
cp -r ../cad/STLs/blenderRoom_020623/* hr12b/constant/triSurface
```


## Navigation 
``` bash
# see path for present/current working directory 
pwd

# list directories and the things w/in them (think only goes one level down?)
ls * 

# list all files and directories (including hidden)
ls -a 


# details about files in a directory (like when they were last edited) 
ls -l
```


## Jobs 
``` bash 
# print all jobs with PID 
ps S

# print all jobs running in terminal 
ps -r

# kill job with certain pid 
kill -9 <job pid number>

# kill job with certain name in command 
pkill -9 -f gnuplot

# ps options 
ps --help simple 
```

## Untarring Files 
``` bash
# untarring files
tar -xvf archive.tar.gz
gunzip file.txt.gz

```

## Find and Delete Files 
```bash 
find and delete files 

# first find files (in current path, can replace . with real path)
find . -name '*py*'

# then delete as needed
find . -name '*py*' -delete

# all files with numbers
find . -name "[0-9]*"

find . -name '*([0-5][0-9]|6[0-7])*'

find <path> -type f | grep -E "/myfile_([0-9]|[0-5][0-9]|6[0-7])\.log$"

# files between 1 and 59? -> cant get delete to work, but this is recursive 
find . -type d | grep -E "([1-5]|[1-5])"
find .|  grep -E "([1-5]|[1-5])"



```
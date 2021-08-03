# THOMAS docker version
This is a docker container for the thalamic nuclei segmentation method Thalamus Optimized Multi Atlas Segmentation (THOMAS)
The THOMAS workflow is shown below-

![THOMAS workflow](THOMAS.jpg "Workflow")

## Installation instructions
- Download the files using ```git clone https://github.com/thalamicseg/thomasdocker.git``` which will create a thomasdocker directory

- Make sure docker is installed already on your machine. Then run ```docker build -t thomas .``` inside the thomasdocker directory to create a container named thomas. Note the period at the end of the command which is critical.

- The container is 41Gb so make sure the disk/partition you are installing it in has enough space. During buildtime, it might need more for temporary files so use 100G to be safe. The required programs for THOMAS including ANTs (2.3.4), FSL (5.0), c3d and THOMAS pulled from github will be downloaded which are long. So please be patient !

- When the build finishes, type ```docker images``` to see thomas listed as a repository. You are good to go !

## Script usage
- THOMAS is installed in the container in /opt/thomas_new and comes with three wrapper scripts.
- Use the thomas_csh wrapper provided for WMn MPRAGE data (or thomas_csh_big for handling large ventricles such as in older subjects)
  
  Usage: ```thomas_csh WMnMPRAGEfile <ro/lo>```  or ```thomas_csh_big WMnMPRAGEfile <ro/lo> ```

  **Note 1**: the first argument is the white matter nulled MPRAGE file in NIFTy nii.gz format. Make sure it is just the file name and not a full path (e.g. wmn.nii.gz not ~foo/data/case1/wmn.nii.gz. Basically, run the script in the directory where the file is located. If you have each subject in a directory, go to each directory and call the thomas_csh script, usually from a simple csh or bash script
  
  **Note 2**: the second argument, if set to ro/lo, would only segment the right/left side (if missing, it defaults to both left and right)
- Use the thomas_csh_mv wrapper provided for standard MPRAGE or T1 (FSPGR or BRAVO in older GE) data

  Usage: ```thomas_csh_mv MPRAGE/T1file <ro/lo>``` 
  
## Docker usage
- There are multiple ways to use the container. You can launch a container and mount the folder with your data inside the container and then run it manually or a script inside the container. You can also directly run it from the command line by launching the container with a bash command.
- Method 1. Launch the container using ```docker run -v <yourdatadir>:<yourdatadir> -w <yourdatadir> --user $(id -u):$(id -g) --rm -it thomas ``` and then run the thomas_csh or thomas_csh_mv on the files you want. Each file has to be in a separate folder and you can simply write a script to go into each one and run the script.
- Method 2. Launch the container using ```docker run -v ${PWD}:${PWD} -w ${PWD} --user $(id -u):$(id -g) --rm -t thomas bash -c "thomas_csh_mv T1filename" ``` to directly run thomas_csh_mv on the T1filename located in the current directory. Note that you will have to have each file in a different directory and write a script to go into each directory and execute the above command. 

## Permissions
- After you run inside the docker, the files created will be owned by root which can be annoying if you want to delete them and do not have sudo. To avoid this you can, add ```--user $(id -u):$(id -g) ``` to your docker commands to preserve the ownership. This, in some machines, will generate a bizarre prompt due to usernames missing but after it finishes, it will retain your ownership of the files it creates

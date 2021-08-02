# THOMAS docker version
This is a docker container for the thalamic nuclei segmentation method Thalamus Optimized Multi Atlas Segmentation (THOMAS)
The THOMAS workflow is shown below-

![THOMAS workflow](THOMAS.jpg "Workflow")

## Installation instructions
- Download the files using ```git clone ``` 

- Make sure docker is installed already on your machine. Then run ```docker build -t thomas .``` to create a container named thomas. Note the period at the end of the command which is critical.

- The container is 41Gb so make sure the disk/partition you are installing it in has enough space. During buildtime, it might need more for temporary files so use 100G to be safe. The required programs for THOMAS including ANTs (2.3.4), FSL (5.0), c3d and THOMAS pulled from github will be downloaded which are long. So please be patient !

- When the build finishes, type ```docker images ``` to see thomas listed as a repository. You are good to go !

## Script usage
- THOMAS is installed in the container in /opt/thomas_new and comes with three wrapper scripts.
- Use the thomas_csh wrapper provided for WMn MPRAGE data (or thomas_csh_big for handling large ventricles such as in older subjects)
  
  Usage: ```thomas_csh WMnMPRAGE_file <ro/lo>```  or ```thomas_csh_big WMnMPRAGE_file <ro/lo> ```

  Note 1: the first argument is the white matter nulled MPRAGE file in NIFTy nii.gz format. Make sure it is just the file name and not a full path (e.g. wmn.nii.gz not ~foo/data/case1/wmn.nii.gz. Basically, run the script in the directory where the file is located. If you have each subject in a directory, go to each directory and call the thomas_csh script, usually from a simple csh or bash script
    
  Note 2: the second argument if set to ro/lo would only segment the right/left side (if missing, it defaults to both left and right)
- Use the thomas_csh_mv wrapper provided for standard MPRAGE or T1 (FSPGR or BRAVO in older GE) data

  Usage: ```thomas_csh_mv MPRAGEorT1_file <ro/lo>``` 

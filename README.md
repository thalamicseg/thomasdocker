# THOMAS docker version
This is a docker container for Thalamus Optimized Multi Atlas Segmentation (THOMAS), the state of the art multi-atlas thalamic nuclei segmentation method.
The THOMAS workflow is shown below:-

![THOMAS workflow](THOMAS.jpg "Workflow")

## Installation instructions
- Download the files using ```git clone https://github.com/thalamicseg/thomasdocker.git``` which will create a **thomasdocker** directory

- Make sure docker is installed already on your machine or install it from here https://docs.docker.com/get-docker/.  
- Run ```docker build -t thomas .``` inside the thomasdocker directory after the download in step 1 to create a container named thomas. Note the period at the end of the command which is critical.

- The container is 41Gb so make sure the disk/partition you are installing it in has enough space. During buildtime, it might need more for temporary files so use 100G to be safe. The required dependencies i.e. ANTs (2.3.4), FSL (5.0), c3d, and THOMAS code from github will be all downloaded which will take time. So please be patient !

- When the build finishes, type ```docker images``` to see thomas listed as a repository. If you see it, you are good to go !

  
## Docker usage
- To run THOMAS, **each anatomical T1 or WMn MPRAGE file should be in a separate directory**. You can launch the container from the command line by running the following command inside the directory containing the T1.nii.gz file:
 ```docker run -v ${PWD}:${PWD} -w ${PWD} --rm -t thomas bash -c "thomas_csh_mv T1.nii.gz" ```. The -v argument bind mounts your data directory inside the container. Change the T1.nii.gz to your desired filename. If you specify an additional lo or ro after the file name, it will restrict to left or right side only. If missing, it will run for both sides.
- Note that there are three scripts that can be run-
    - thomas_csh_mv is used for standard MPRAGE or T1 (FSPGR or BRAVO on older GE scanners) data as in the example above
    - thomas_csh is used for WMn MPRAGE aka FGATIR data
    - thomas_csh_big is also for WMn MPRAGE/FGATIR data but used for older patients with enlarged ventricles (larger crop)
- Note: If you are running on a Linux host, output will be owned by root unless you add ```--user $(id -u):$(id -g) ``` to your docker commands to preserve the ownership. This, in some machines, will generate a bizarre prompt due to usernames missing but after it finishes, it will retain your ownership of the files THOMAS creates.
- Advanced users can also launch just the container using ```docker run -v <yourdatadir>:<yourdatadir> -w <yourdatadir>  --rm -it thomas ``` and then run the thomas_csh or thomas_csh_mv on the desired files inside the container. This allows some flexibility in manipulating things inside the container.
- **Updated Jun 1 2022** Two wrapper bash scripts thomas and thomast1 are now available which does everything including the ownership stuff (see usage below). You should put them in ~/bin (and add ~/bin to your path to easily access it from anywhere or call ~/bin/thomas or ~/bin/thomast1)

## Running the provided test data 
-  First extract the test data by running ```tar -xvzf example.tgz``` inside the **thomasdocker** directory
-  If using wrapper scripts, move thomas and thomast1 from inside thomasdocker to ~/bin
-  For the WMn MPRAGE data in thomasdocker, you can run ```docker run -v ${PWD}:${PWD} -w ${PWD} --user $(id -u):$(id -g) --rm -t thomas bash -c "thomas_csh WMn.nii.gz" ```  or ```~/bin/thomas WMn.nii.gz``` 
-  For the T1 MPRAGE data in thomasdocker, you can run ```docker run -v ${PWD}:${PWD} -w ${PWD} --user $(id -u):$(id -g) --rm -t thomas bash -c "thomas_csh_mv T1.nii.gz" ``` or ```~/bin/thomast1 T1.nii.gz```
-  Note: you need to create separate subdirectories ideally outside thomasdocker to run WMn and T1 test data or they will get overwritten

## Outputs
The directories named **left** and **right** contain the outputs which are individual nuclei labels (e.g. 2-AV.nii.gz for anteroventral and so on), nonlinear warp files, and also the following files:
- **thomas.nii.gz** is all labels fused into one file (same size as the cropped anatomical file)
- **thomasfull.nii.gz** is also fused labels but same size as the input file (i.e. full size as opposed to cropped)
- **nucVols.txt** contains the nuclei volumes in mm^3 
- **regn.nii.gz** is the custom template registered to the input image. This file is critical for debugging. Make sure this file and cropped input file are well aligned. For the right side, do this comparison for crop_<input file> and regn.nii.gz inside the tempr directory as there is a flip operation performed for the right.
- For right side, the labels are called **thomasr.nii.gz** and **thomasrfull.nii.gz**
- **temp** and **tempr** directories contain intermediate step files which sometimes can help debugging esp for right side registration
- A reminder that outputs will be overwritten if run again in the same directory. So if you want to process multiple files, put them in separate directories and run a simple shell script to loop through all the folders. 

## Thalamic nuclei expansions and label definitions
THOMAS outputs the mammillothalamic tract (14-MTT) and the eleven delineated nuclei grouped as follows (Note that 6-VLP is further split into 6_VLPv and 6_VLPd. 6_VLPv is roughly concordant with VIM used for targeting in DBS applications although differences seem to exist between Morel and Schaltenbrand atlases)-

	(a) medial group: habenula (13-Hb), mediodorsal (12-MD), centromedian (11-CM) 
	(b) posterior group: medial geniculate nucleus (10-MGN), lateral geniculate nucleus (9-LGN),  pulvinar (8-Pul),
	(c) lateral group: ventral posterolateral (7-VPL), ventral lateral posterior (6-VLp), ventral lateral anterior (5-VLa), ventral anterior nucleus (4-VA)
	(d) anterior group: anteroventral (2-AV)


## Citation
The original Neuroimage paper on THOMAS can be found here https://pubmed.ncbi.nlm.nih.gov/30894331/

	Su J, Thomas FT, Kasoff WS, Tourdias T, Choi EY, Rutt BK, Saranathan M. Thalamus Optimized Multi-atlas Segmentation (THOMAS):
	fast, fully automated segmentation of thalamic nuclei from anatomical MRI. _NeuroImage_; 194:272-282 (2019)

THOMAS adapted for standard T1 contrast is described here https://pubmed.ncbi.nlm.nih.gov/34024824/

	Bernstein A, Rapcsak SZ, Hornberger M, Saranathan M, ADNI collective. Structural changes in thalamic nuclei across clinical and
	prodromal Alzheimer's disease. _J. Alzheimer's Dis_; 82:361-371 (2021)


## Contact
Please contact Manoj Saranathan manojsar@email.arizona.edu in case you have any questions or difficulties in installation/running or to report bugs/issues. 


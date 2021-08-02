# THOMAS docker version
This is a docker container for the thalamic nuclei segmentation method Thalamus Optimized Multi Atlas Segmentation (THOMAS)
The THOMAS workflow is shown below-

![THOMAS workflow](THOMAS.jpg "Workflow")

## Installation
Download the files using ```git clone ``` 

Make sure docker is installed already on your machine. Then run ```docker build -t thomas . ```.

Note the container is 41Gb so make sure the partition you are installing it in has enough space. During buildtime, it might need more for temporary files so use 100G to be safe.

The required programs for THOMAS including ANTs (2.3.4), FSL (5.0), c3d and THOMAS pulled from github will be downloaded which are long. So please be patient !

## Usage

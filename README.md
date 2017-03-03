# Alzheimer's disease skull reconstruction
Repository for reconstruction of simulated skull CT data for AD project with files for reconstruction simulated skull CT data (KTH/SSF AD project)

## Usage
###### Load data to disc
In order to use this repo, you first need to copy the data to your local machine. This can be done by:
```
$ python -c "import adutils; adutils.load_data_from_nas('Z:\\')"
```
where `'Z:\\'` should be replaced with your local path to the data drive of the NAS. This takes quite some time (~10 minutes) to run at first, but makes subsequent reconstructions much faster. Note that it uses ~6GB of disk space in a subfolder to this project.

Files to read and reconstruct the data is given in this repository (the easiest example is given by a FBP reconstruction in [`FBP_reco_skullCT.py`](FBP_reco_skullCT.py). Most of the data handling is however hidden in [`adutils.py`](adutils.py). To make use of these, simply run the following in your script
```
import adutils
```  

###### Rebin data 
To avoid heavy computations, a suggestion is to use downsampled rebinned data. To rebin data, run the following after you've downloaded data to your local drive:
``` 
$ python -c "import adutils; adutils.rebin_data(10)"
```
with desired ``` (rebin_factor)``` (default is set to 10). Once you've done this, simply load your data using the rebin data flag, as per below:
``` 
rebin_factor = 10

# Discretization
reco_space = adutils.get_discretization()

#Forward operator
A = adutils.get_ray_trafo(reco_space, use_rebin=True, rebin_factor=rebin_factor)

# Data
rhs = adutils.get_data(A, use_rebin=True, rebin_factor=rebin_factor)
```

###### Save data
To save data in a format that the clinical can review (typically [nifti](https://nifti.nimh.nih.gov/nifti-1)), use the ```adutils.save_data``` utility, with ```x``` being your reconstruction

```
fileName = /my/path/myFile
adutils.save_data(x, fileName, as_nii=True, as_npy=True)
```

###### 2D data set
To be able to work with a smaller dataset (to e.g. try out different reconstruction parameters), a 2D fanbeam dataset has been generated, consiting of a mid transversal slice of the skull. To use this dataset, simply use the flag ```use_2D = True``` in the appropriate calls to ```adutils```. This means e.g.:
``` 
# Discretization
reco_space = adutils.get_discretization(use_2D=True)

#Forward operator
A = adutils.get_ray_trafo(reco_space, use_2D=True)

# Data
rhs = adutils.get_data(A, use_2D=True)
```
Exampes of such is given for [FBP](https://github.com/davlars/ad-skull-reconstruction/blob/master/FBP_reco_skullCT_2D.py), [CGLS](https://github.com/davlars/ad-skull-reconstruction/blob/master/CGLS_reco_skullCT_2D.py), and [TV](https://github.com/davlars/ad-skull-reconstruction/blob/master/TV_reco_skullCT_2D.py).


## Raw data

All simulated data is available on the lcrnas. For high dose (150 mGy) skullCT (following settings used at KI Geriatrics), data can be found in:
```
/lcrnas/data/Simulated/120kV
```
The simulated data consists of 23 spiral turns, and is divded in 23 separate files. The data is given as:
```
HelicalSkullCT_70100644Phantom_no_bed_Dose150mGy_Turn_{0,1,2,3,...,22}.data.npy
```
with corresponding geometry pickled in
```
HelicalSkullCT_70100644Phantom_no_bed_Turn_{0,1,2,3,...,22}.geometry.p
```  
Note: Data with lower dosage is available upon request. All simulated data is given in ```/lcrnas/data/Simulated/120kV/raw/```. **DO NOT change the data in this repository**. If unsure, ask. 

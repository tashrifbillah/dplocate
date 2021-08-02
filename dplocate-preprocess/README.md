# DPLocate Step 2: Preprocess the GPS data with temporal filtering

## Table of contents

1. [Requirements](#requirements)
2. [Installation](#installation)
3. [Usage](#usage)

## Requirements

- The Input to this module is the Output of dplocate-read. The input data is saved as an encrypted
  `file_gps.mat.lock` file.
 
 
- The Data has the following format:
    - Six vector variables in MATLAB with the following names and contents:

```text
t1:   timestamp(JAVA time)
u1:   UTCtime(YYYY-MM-DDThh:mm:ss:mmm)
lat1: latitude(deg)
lon1: longitude(deg)
alt1: altitude(deg)
acc1: accuracy(m)
```

- The output directory can be edited. The default is `./processed/gps_dash2` and the 
  file is saved as `dash.mat.lock`.
  
- This step applies temporal filtering by defining 'epochs' 
  and saves the summary of the epochs; as the following columns:
  
  ![image](https://user-images.githubusercontent.com/69796473/124317866-d81df380-db45-11eb-9db0-262e0a6e4044.png)


- After this step run dplocate-process pipeline

## Installation

To install `dplocate-preprocess` on your system

```bash
git clone git@github.com:harvard-nrg/dplocate-preprocess.git 
cd dplocate/preprocess
pip install -r requirements.txt
```
### Passphrase
For files that are locked, please provide a passphrase by setting the 
`BEIWE_STUDY_PASSCODE` environment variable.
For example:
```
export BEIWE_STUDY_PASSCODE='test passcode 1 2 3'
```

## Usage

The default is that the pipeline runs for the `new` files.

```bash

# To generate reports for subject A and subject C in STUDY_PILOT under their processed folders
# Define the PHOENIX, consent and MATLAB directories 
preprocess_gps_mc.py --phoenix-dir [PHOENIX DIR] --consent-dir [CONSENT DIR] --matlab-dir [MATLAB DIR] --study STUDY_PILOT --data-type phone --pipeline preprocess_gps_mc --include active --data-dir PROTECTED --subject A C

```

For more information, please run

```bash
preprocess_gps_mc.py -h
```

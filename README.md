# DPLocate Modules
# DPLocate-read(DPLocate Step 1): Extract the raw GPS data

## Table of contents
1. [Requirements](#requirements)
2. [Installation](#installation)
3. [Usage](#usage)

## Requirements
- The Raw data is saved as encrypted hourly `.csv.lock` files with the following naming 
  format

```text
YYYY-MM-DD hh-mm-ss.csv.lock
```

- The Data has the following format:
    - GPS data from row 1 with the following columns:

```text
timestamp(JAVA time)|UTCtime(YYYY-MM-DDThh:mm:ss:mmm)|latitude(deg)|longitude(deg)|
altitude(deg)|accuracy(m)
```

- The output directory can be edited. The default is `./processed/gps_dash2` and the 
  file is saved as `file_gps.mat.lock` which contains six vector variables in MATLAB
  with the following names and contents:

```text
t1:   timestamp(JAVA time)
u1:   UTCtime(YYYY-MM-DDThh:mm:ss:mmm)
lat1: latitude(deg)
lon1: longitude(deg)
alt1: altitude(deg)
acc1: accuracy(m)
```

- This step is the longest part of the pipeline and it can take hours per 
  subject. Running that in parallel for different subjects of a study is 
  recommended to reduce the execution time.

- After this step run dplocate-preprocess pipeline

## Installation

To install `dplocate-read` on your system

```bash
git clone git@github.com:harvard-nrg/dplocate-read.git 
cd dplocate/read
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
parse_gps_mc.py --phoenix-dir [PHOENIX DIR] --consent-dir [CONSENT DIR] --matlab-dir [MATLAB DIR] --study STUDY_PILOT --data-type phone --pipeline parse_gps_mc --include active --data-dir PROTECTED --subject A C

```

For more information, please run

```bash
parse_gps_mc.py -h
```
# DPLocate-preprocess(DPLocate Step 2): Preprocess the GPS data with temporal filtering

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

# DPLocate-process(DPLocate Step 3): Process the GPS data with clustering (every 150 days for long studies)

## Table of contents
1. [Requirements](#requirements)
2. [Installation](#installation)
3. [Usage](#usage)

## Requirements
- The Input to this module is the Output of dplocate-preprocess. 
  The input data is saved as an encrypted `dash.mat.lock` file.

- The Data has the following format:
    - Epoch data from row 1 with the following columns:
![image](https://user-images.githubusercontent.com/69796473/124317501-46ae8180-db45-11eb-8b34-07f408fc09df.png)

- The output directory can be edited. The default is `./processed/gps_dash2` and the 
  file is saved as `daily_nr#.mat.lock` where # is a natural number from 1 to inf.

- This step applies spatial clustering to the 'epochs' and saves the daily maps and the 
  coordinates of the Points of Interest (PoIs).
- After this step run dplocate-aggregate pipeline

## Installation
To install `dplocate-process` on your system

```bash
git clone git@github.com:harvard-nrg/dplocate-process.git 
cd dplocate/process
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
```bash
# To generate reports for subject A and subject C in STUDY_PILOT under their processed folders
# Define the PHOENIX, consent and MATLAB directories 
process_gps_mc.py --phoenix-dir [PHOENIX DIR] --consent-dir [CONSENT DIR] --matlab-dir [MATLAB DIR] 
--study STUDY_PILOT --data-type phone --pipeline process_gps_mc --include active --data-dir PROTECTED --subject A C

```

For more information, please run
```bash
process_gps_mc.py -h
```
# DPLocate-aggregate(DPLocate Step 4): Aggregate the processed daily maps of the study

## Table of contents
1. [Requirements](#requirements)
2. [Installation](#installation)
3. [Usage](#usage)

## Requirements
- The Input to this module is the Output of dplocate-process. 
  The input data is saved as encrypted `daily_nr#.mat.lock` files.

- The output directory can be edited. The default is `./processed/gps_dash2` and the 
  file is saved as `daily_all.mat.lock`.

- This step aggregates the daily maps of the 150-day clusters.
- 
- After this step run dplocate-plot pipeline

## Installation
To install `dplocate-aggregate` on your system
```bash
git clone git@github.com:harvard-nrg/dplocate-aggregate.git 
cd dplocate/aggregate
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
```bash
# To generate reports for subject A and subject C in STUDY_PILOT under their processed folders
# Define the PHOENIX, consent and MATLAB directories 
aggregate_gps_mc.py --phoenix-dir [PHOENIX DIR] --consent-dir [CONSENT DIR] --matlab-dir [MATLAB DIR] 
--study STUDY_PILOT --data-type phone --pipeline aggregate_gps_mc --include active --data-dir PROTECTED --subject A C
```

For more information, please run
```bash
aggregate_gps_mc.py -h
```

# DPLocate-plot(DPLocate Step 5): Plot color-coded GPS daily map

## Table of contents
1. [Requirements](#requirements)
2. [Installation](#installation)
3. [Usage](#usage)

## Requirements
- The Input to this module is the Output of dplocate-plot. 
  The input data is saved as encrypted `daily_all.mat.lock` files.

- The output directory can be edited. The default is GENERAL directory `/phone/processed/mtl_plt` and the 
  file is saved as `STUDY_SUBJECT-tmzn.png.`.

- This step plots the daily maps and transfers the processed data into the GENERAL folder.


## Installation
To install `dplocate-plot` on your system
```bash
git clone git@github.com:harvard-nrg/dplocate-plot.git 
cd dplocate/plot
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

```bash
# To generate reports for subject A and subject C in STUDY_PILOT under their processed folders
# Define the PHOENIX, consent and MATLAB directories 
phone_gps_mc.py --phoenix-dir [PHOENIX DIR] --consent-dir [CONSENT DIR] --matlab-dir [MATLAB DIR] 
--study STUDY_PILOT --data-type phone --pipeline phone_gps_mc --include active --data-dir PROTECTED --subject A C
```

For more information, please run
```bash
phone_gps_mc.py -h
```

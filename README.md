# Comprehensive IFRC Data Analysis System (CIDAS) User Manual

## Introduction:
Welcome to CIDAS. This user manual aims to help developers use our product 
effectively. CIDAS is a powerful tool for downloading, processing and analysing 
the data from DesInventar. With its robust set of features and intuitive APIs, 
you can analyse the data quickly and easily.

## System Requirements:
- Linux / macOS / Windows with access to command line tools (CLI).
- Good Internet connection.
- 8 GB RAM or higher for all modules except data-downloader.RecordsConverter. 
  64 GB RAM for data-downloader.RecordsConverter
- 4 GB free disk space for storing the data.

## Installation:
To install CIDAS, please follow the 
[deployment manual](https://github.com/COMP0016-IFRC-Team5/.github/Deployment-Manual.md) 
for detailed instructions.

## Getting Started:
- Processed data has been put into the data folder under data-visualiser. If 
  you don’t need to update the data, you can directly go to the data-visualiser 
  directory to generate exceedance curves and tables.  
- If you want to update and process the data, you need to set the data folder 
  under the data-visualiser directory as the target folder for all three 
  modules.  
- There is a dependency between the three modules. The data-visualiser module 
  is dependent on the data processed by the data-processor. The data-processor 
  is dependent on the data downloaded and converted by data-downloader.

## Using CIDAS

### The data-downloader module

#### 1. Activate the environment
```bash
conda activate data-downloader
```

#### 2. Usage
This module provides functionality for downloading data from DesInventar to the 
target directory.  
- You need to download xml and csv files from DesInventar using its 
  corresponding module. 
- Then you need to convert xml to csv using the `record_converter` module. This 
  step may use 60 GB of memory.
- Finally, you can use the `categoriser` module to generate categorisations for 
  the events.

#### Note for the `csv_downloader` module
In this code snippet, `target_dir` is the directory where the csv files will be 
downloaded to.
```python
import csv_downloader
csv_downloader.start_download(target_dir='./data', mode=0b000)
```
`mode` is an integer from 0 to 7, whose highest bit determines whether to 
ignore existing spreadsheets and last two bits determine the level of ignoring 
of caches.

Let `ignore_cache = mode & 0b011`. If `ignore_cache` is greater than 0, the 
crawler will ignore cache in `caches/disasters.pkl`. If `ignore_cache` is 
greater than 1, the crawler will ignore cache in `caches/disasters.pkl` and 
`caches/disasters/*`. If `ignore_cache` is greater than 2, all caches will be 
ignored.

#### Example
See `example.py` for detail.

### The data-processor module

#### Activate the environment
```bash
conda activate data-processor
```

#### Usage
This module provides functionality for processing data from a data directory.

##### APIs
```
set_data_dir(data_dir)
    Set the data directory to be used by the processor.

process(option)
    Process the data in the data directory.
```

##### Detailed information
To use this module, first call `set_data_dir()` to set the data directory to be
used by the processor. Then call `process()` with a dictionary `option`
containing the following keys:
```
* 'desinventar': A dictionary containing the following keys:
    - 'merge': A boolean indicating whether to merge data.
    - 'slice': A boolean indicating whether to slice data.
* 'emdat': A dictionary containing the following key:
    - 'process': A boolean indicating whether to process EMDAT data.
```

#### Example:
See `example.py` for details.

### The data-visualiser module

#### Activate the environment
```bash
conda activate data-visualiser
```
#### Usage
##### To run the example
The example shows a typical case which produce the return period - deaths & 
affected people graphs for floods and earthquakes in Albania and Pakistan. Data 
used from the past 15 years.

```bash
python example.py
```
A typical process could be done in 3 steps:
1. set data folder path
2. plot graph(s)
3. get table(s)


###### 1. Set input data

To use default processed data:

```python
visualiser.set_data_folder('./data')
``` 
Then you can get the available countries for analysis by
calling [`visualiser.get_available_countries()`](https://github.com/COMP0016-IFRC-Team5/data-visualiser/blob/main/example.py#L5)
after setting the data folder.

```python
print(visualiser.get_available_countries())
```

###### 2. Plot graph(s)

API for plot exceedance curves:
```
visualiser.plot_exceedance_curves(
    countries,
    events,
    losses,
    years_required
)
```
Args:
- countries: A string or list of strings specifying the countries. 
- events: A string or list of strings specifying the events. 
- losses: A Loss enum or list of Loss enums specifying the losses. 
- years_required: An int specifying the maximum number of years of data 
  required. Default is -1.


###### 3. Get table(s)
The tool also provide a function to extract key return period for all metrics 
defined and organised as a table. The table can be easily accessed by calling 
[`visualiser.get_exceedance_table()`](https://github.com/COMP0016-IFRC-Team5/data-visualiser/blob/main/example.py#L22):

```
tables = visualiser.get_exceedance_table(
    countries,
    events,
    years_required
)
```

## Customising CIDAS

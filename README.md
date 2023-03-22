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
[deployment manual](https://github.com/COMP0016-IFRC-Team5/.github/blob/main/Deployment-Manual.md) 
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
* 'desinventar': A dictionary containing the following keys:
    - 'merge': A boolean indicating whether to merge data.
    - 'slice': A boolean indicating whether to slice data.
* 'emdat': A dictionary containing the following key:
    - 'process': A boolean indicating whether to process EMDAT data.

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

# Customising CIDAS

## data-downloader

### `xml_downloader`

The country information was obtained from the DesInventar Download page
(https://www.desinventar.net/download.html). If you want to maintain the list
of the countries, you need to manually go the webpage and inspect the
hyperlinks to get its country code.

For example, for Comoros, the html tag is
```html
<a href="/DesInventar/download_base.jsp?countrycode=com" target="_blank">
    Comoros
</a>
```
Its country code is `com`.

The code containing country information is located in
`xml_downloader/_country_info.py`. If DesInventar adds a country in the future,
with name `CountryName` and country code `ctn`, then you need to append `ctn`
to the list `country_code` and append `CountryName` to the list `country_name`.

### `csv_downloader`
You can delete statement `remove_empty_databases()` in `start_clean()` function
of `_cleaner.py` if you don't want to delete empty csv files (The contents of
the files are not used).

#### Future development
After running `__get_country_disaster_dict()` in `_csv_crawler.py`, we have
disaster types acquired from DesInventar. Therefore, there is no need to
download the csv files with disaster type. However, changes need to be made for
`categoriser` to adapt the new way of acquiring the disaster types rather than
from disk.

### `categoriser`
Categorisation information is stored in the `_categorisations.py`. If you want
to move some subtypes to another major type, you need to modify this file.

### `record_converter`
Currently, the record converter reads the entire xml file into the memory.
Therefore, for large xml files like `Sri Lanka.xml` (1.2 GB), it may take more
than 60 GB of RAM to process this file.

For future development, you may want to change it to parse the file element by
element. Here is the information you may need:
- The records of an xml file are under `DESINVENTAR.fichas` with tag name `TR`.
- Once you have the tag for a record, you can use `RecordFactory.from_tag()` to
  generate a `Record` to you.
- After you get the full list of the records, you can use
  `list(map(lambda rec: rec.as_dict(), records))` to obtain the converted
  records as a list of dictionary.
- Finally, you can use `df = pd.DataFrame.from_dict(converted_records)` to get
  a pandas dataframe for all of the records. The rest of the work will be done
  by `__convert_database()` in `_main.py`.


## data-processor:

When implementing the algorithm for merging the records to events, we referred
[THE HYBRID LOSS EXCEEDANCE CURVE](https://www.preventionweb.net/english/hyogo/gar/2011/en/bgdocs/ERN-AL_2011.pdf).
In section 4.2.1 Algorithm for grouping events together. The code related to
the implementation is located in `processor/_models/_event_builder.py` and
`processor/_apps/_combiner.py`.

### Slice
The slicing algorithm is `__slice_for_one_event()` in
`processor/_apps/_slicer.py`. Currently, we just slice out the first 5% of the
events.

## data-visualiser:

### Add loss metrics
Currently, we only defined deaths and affected people (directly affected +
indirectly affected). If you want to add more metrics, you can modify it at
`visualiser/_models/_loss.py`.

### Change folder to conduct analysis
In `visualiser/_config.py`, you can modify `__SELECTED_FOLDER` to the folder
that you want to conduct analysis.

### Change labels and highlight points
You can find relevant code in `__add_label()` method and `__highlight()` method
for `Plotter` class

### Add new data source
If you want to use another data source, you need to put the data source under
the `data` directory and ensure the folder structure is:
```text
data-visualiser/
├─ data/
│  ├─ new_data_source/
│  │  ├─ country_name/
│  │  │  ├─ EARTHQUAKES.csv
│  │  │  ├─ FLOODS.csv
│  │  │  ├─ STORMS.csv
```
For each csv file, the data should be parsed to contain these columns: `deaths`,
`directly_affected`, `indirectly_affected`, `start_date`, and `secondary_end`.  
For example:

| deaths | directly_affected | indirectly_affected	 | start_date | secondary_end	 |
|--------|-------------------|----------------------|------------|----------------|
| 0      | 100               | 200               	  | 1911-02-18 | 1911-02-21     |
| 5      | 60                | 300               	  | 1912-02-18 | 1912-02-21     |
| 3      | 100               | 100               	  | 1914-02-18 | 1914-02-21     |
| 10     | 220               | 400               	  | 1916-02-18 | 1916-02-21     |

Next, you need to add a member in `visualiser/_adapters/_folders.py` with value
being the name of the data source folder.

Then, you need to modify `__SELECTED_FOLDER` in `_config.py`.

Note: you need to ignore or remove the labels after plot the curves if you are
working with new data sources.

# Troubleshooting
* Check whether you have correctly set the data folder and the folder is not 
  empty
* Check whether you have a stable internet connection when using data-downloader
* Check whether you have read and write access to the folder you set to be the 
  data folder.
* Check if you have followed README in each repository.

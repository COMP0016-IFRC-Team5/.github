# Comprehensive IFRC Data Analysis System (CIDAS)

These repositories contain the code for the CIDAS. The system is composed of 
three components:

- [Data Downloader](https://github.com/COMP0016-IFRC-Team5/data-downloader)

- [Data Processor](https://github.com/COMP0016-IFRC-Team5/data-processor)

- [Data Visualiser](https://github.com/COMP0016-IFRC-Team5/data-visualiser)

## Introduction

The CIDAS is a system that allows the IFRC to analyse data from the DesInventar 
and EM-DAT databases. 

The system is composed of three components:

- Data Downloader: This component downloads data from the DesInventar and 
  is able to convert it into a format that is compatible with the Data 
  Processor. It also can generate disaster categorisations from downloaded data 
  for use in the Data Processor.
- Data Processor: This component processes data from the DesInventar and 
  EM-DAT databases. It can aggregate the data and slice the data.
- Data Visualiser: This component visualises the processed data. It can 
  generate loss exceedance graphs and tables.

This [deployment manual](https://github.com/COMP0016-IFRC-Team5/.github/blob/main/Deployment-Manual.md) 
provides step-by-step instructions for deploying our application. It assumes 
that you have already completed the necessary preparations, such as setting up 
the networks and installing required pieces of software.

The user manual for the CIDAS can be found 
[here](https://github.com/COMP0016-IFRC-Team5/.github/blob/main/User-Manual.md).
This manual provides instructions on how to use and customise the CIDAS.





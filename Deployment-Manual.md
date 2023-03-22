# Deployment Manual

## Introduction

This deployment manual provides step-by-step instructions for deploying
our application. It assumes that you have already completed the
necessary preparations, such as setting up the networks and installing
required pieces of software.

## System Requirements

To deploy the web application, you will need the following:

-   Git
-   Anaconda / Miniconda / Python version 3.10 or higher
-   Access to the command line interface (CLI)

## Deployment Steps

**Step 1:** Clone the repositories in sequence in preferred directory

Clone data-downloader

    git clone https://github.com/COMP0016-IFRC-Team5/data-downloader.git

Clone data-processor

    git clone https://github.com/COMP0016-IFRC-Team5/data-processor.git

Clone data-visualiser

    git clone https://github.com/COMP0016-IFRC-Team5/data-visualiser

**Step 2:** Create conda environment

data-downloader:

    cd data-downloader

    conda env create -f conda_env.yml

    cd ..

data-processor:

    cd data-processor

    conda env create -f conda_env.yml

    cd ..

data-visualiser:

    cd data-visualiser

    conda env create -f conda_env.yml

    cd ..

After these steps, it should create three conda environments:
data-downloader, data-processor, data-visualiser.

## Troubleshooting

If you encounter any issues during the deployment process, here are a
few troubleshooting tips:

-   Make sure you have read and write permissions for the directory
-   Check you have the access to the Internet

## Uninstall

If you want to uninstall the application, you need to do the following
steps:

**Step 1:** Remove conda environments

    conda remove -n data-downloader -all
    conda remove -n data-processor -all
    conda remove -n data-visualiser -all

**Step 2:** Delete cloned repositories

    rm -rf data-downloader
    rm -rf data-processor
    rm -rf data-visualiser

## Contact Information

If you need technical support or have any questions about deploying the
application, please contact our team at 
[dekun.zhang.20@ucl.ac.uk](mailto:dekun.zhang.20@ucl.ac.uk) or 
[dekun.zhang.2002@gmail.com](mailto:dekun.zhang.2002@gmail.com).

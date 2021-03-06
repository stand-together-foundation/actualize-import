---
title: "Data Imports for Project Actualize"
output: 
  rmarkdown::html_vignette: 
    keep_md: yes
vignette: >
  %\VignetteIndexEntry{Data Imports for Project Actualize}
  %\VignetteEngine{knitr::rmarkdown}
  %\VignetteEncoding{UTF-8}
---



## Prerequisites

In order to access data as described in this tutorial, you will need 
1. Access to data stored in the impact team's Google Drive, 

2. Access to the statistical programming language R, and ideally RStudio as an IDE, and 

3. Within R, the current version of the `googledrive` and `dotenv` packages. 

## Data Storage

For the time being all production measurement data will be stored using a Google Drive directory to allow for secured sharing with external organizations with a need to access our data. This is a temporary solution while we establish a formal database with access controls and other security protocols related to STF Impact data resources. 

Raw data are stored at the following [link](https://drive.google.com/drive/u/0/folders/1rYTPZUaRXz6j-MerITWoXaaXXVDlKDBn), with access to the data controlled by Joe Mienko or Tyler Carolan.

Within the `actualize-data` directory, data sets are stored by engagement, with the directory named after the engagement (e.g. LCLC), and the raw data stored within that top-level directory. So, for LCLC, data are stored in `My Drive\actualize-data\LCLC\60 Decibels_LCLC Stand Together Anonymized Raw Data_July 2021.csv`. 

-- The root directory (`My Drive`) is common to all Google Drive users. 

-- The shared directory (`actualize-data`) is the root of all shared data.

-- The project directory (`LCLC`) contains the data and any relevant contextual files. In this case, the data are in a file named `60 Decibels_LCLC Stand Together Anonymized Raw Data_July 2021.csv`.

## Data Access

Data can be accessed using the Google Drive UI, or programatically using R. To access using R, you will need to first obtain the "Drive file id" for use in the `drive_get()` function from within the `googledrive` package. The `drive_get()` function gathers metadata from files specified by the "Drive file id". These metadata can be used to download data for local analysis. The easiest way to accomplish this is to obtain the id from the Google Drive UI. The "Drive file id" can be parsed from the URL and passed to the `drive_get()` function, or you can just pass the entire URL to the function and `drive_get()` will parse the id out of the URL for you. 

To prevent duplication of efforts, the Impact team has created an environment file containing URLs to all files used to build the integrated impact database (IID) within the root of the current repository (`.env-file-urls`). This file can be loaded into your local environment using the `dotenv` package which has a single core function that sources environment files into a local R session. The following chunk loads the environment file. 


```r
library(dotenv)

dotenv::load_dot_env(
  file = '.env-file-urls'
)
```

With the environment variables loaded we can load the meta data for each piece of IID source data as shown below. Before we do though, you need to either refresh your authentication token (or download a nmew token). Simply running the `googledrive::drive_auth()` should help ensure that you are properly authenticated across our many stores of data. 


```r
library(googledrive)

drive_auth()
```

From there, you should be able to load the relevant metadata for any of the files with URLs stored in the `.env-file-urls` file. If you want to add a new file to our import process, you should complete the following steps: 

1. Pull a new branch from the `actualize-import` [repository](https://github.com/stand-together-foundation/actualize-import). 

2. Update the `.env-file-urls` with the appropriate `KEY=value` pair. As of this writing, data from organizational engagements have an upper-case key followed by the sequence of the engagement. So, for an organization like Family Promise, the key for the first engagement would be `FAMILY_PROMISE1`, and the key for the second engagement would be `FAMILY_PROMISE2`, etc. The values would be the Google Drive url for the source data. Population-based data keys are named after the geographic scope of the data collection (e.g. `NATIONAL2` in the case of the YouGov data collection from the end of 2021, `NATIONAL1` in the case of the so-called "Amplify" data collection). As of the date of this writing we have three population-based sources: 

    - `NATIONAL1` *The so-called "Amplify" data*
    
    - `NATIONAL2` *National data from the How are Americans doing (HAAD) survey, collected by YouGov*
    
    - `LOCAL1` *Local data strategically chosen based on STF 2022 priorities*
    

3. Update this README to include reference to the new data as needed, and recompile the file locally so that the full import process is completed for old and new data sources. 

4. Push your changes to the remote branch, and make a new PR to a member of the STF measurement team. 

## Metadata

With the relevant URLs read as outlined above, you should be able to grab the metadata needed to complete the imports through the object assignments in the following chunk. 


```r
dbg1 <- drive_get(
  id = Sys.getenv("DBG1")
)

family_promise1 <- drive_get(
  id = Sys.getenv("FAMILY_PROMISE1")
)

yonique1 <- drive_get(
  id = Sys.getenv("YONIQUE1")
)

lclc1 <- drive_get(
  id = Sys.getenv("LCLC1")
)

dfree1 <- drive_get(
  id = Sys.getenv("DFREE1")
)

national1 <- drive_get(
  id = Sys.getenv("NATIONAL1")
)

local1 <- drive_get(
  id = Sys.getenv("LOCAL1")
)

national2<- drive_get(
  id = Sys.getenv("NATIONAL2")
)
```

## Import

The `drive_download()` function can now use the metadata objects to download the raw data locally for further analysis. The code below will download data into a `data` directory within the `actualize-import` repo. The data directory is also referenced within the `.gitignore` file so that data can be utilized locally, but not pushed to git/GitHub when attempting to merge back into the main branch, or collaborate with a colleague. 


```r
drive_download(
  dbg1,
  path = "~/actualize-import/data/dbg1.xlsx", 
  overwrite = TRUE, 
)

drive_download(
  family_promise1,
  path = "~/actualize-import/data/family_promise1.xlsx", 
  overwrite = TRUE
)

drive_download(
  yonique1,
  path = "~/actualize-import/data/yonique1.xlsx", 
  overwrite = TRUE
)

drive_download(
  lclc1,
  path = "~/actualize-import/data/lclc1.xlsx", 
  overwrite = TRUE
)

drive_download(
  dfree1,
  path = "~/actualize-import/data/dfree1.xlsx", 
  overwrite = TRUE
)

drive_download(
  national1,
  path = "~/actualize-import/data/national1.csv", 
  overwrite = TRUE
)

drive_download(
    local1,
    path = "~/actualize-import/data/local1.csv",
    overwrite = TRUE
)

drive_download(
    national2,
    path = "~/actualize-import/data/national2.sav", 
    overwrite = TRUE
)
```



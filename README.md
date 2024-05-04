# project
# Self-Reported Health Analysis

## Project Overview
This project will focus on using public data to explore the significance of “self-reported health” as a health indicator.

## Data Source
1. **NHANES Survey Data**: [1999-2000 Survey Data](https://wwwn.cdc.gov/Nchs/Nhanes/1999-2000/DEMO.XPT)
2. **NHANES Mortality Follow-up Data**: [Linked Mortality Data](https://ftp.cdc.gov/pub/HEALTH_STATISTICS/NCHS/datalinkage/linked_mortality/NHANES_1999_2000_MORT_2019_PUBLIC.dat)
   
## Protocols

- **Step 1) Import the dataset**
  * Survey Data
     * Import the survey data from the 1999-2000 National Health and Nutrition Examination Survey (NHANES):
    ```stata
       import sasxport5 "https://wwwn.cdc.gov/Nchs/Nhanes/1999-2000/DEMO.XPT", clear
    ```
   * Mortality Follow-up Data
      * Obtain follow-up mortality data to analyze over a 20-year period from the National Center for Health Statistics (NCHS). 
   ```stata
   //data
   global mort_1999_2000 https://ftp.cdc.gov/pub/HEALTH_STATISTICS/NCHS/datalinkage/linked_mortality/NHANES_1999_2000_MORT_2019_PUBLIC.dat
   //code
   cat https://ftp.cdc.gov/pub/HEALTH_STATISTICS/NCHS/datalinkage/linked_mortality/Stata_ReadInProgramAllSurveys.do
   ```

- **Step 2) Edit and Rename Provided Script**
  * Download `Stata_ReadInProgramAllSurveys.do`, edit it and rename it to `followup.do'.
  * You may edit it so that it reads in the data directly from the website.
  * Commit the changes with the description “Updated DEMO.XPT linkage .do file".

- **Step 3) Data merging**
  * Merge the survey data with the mortality data, ensuring alignment on the unique sequence numbers:
    ```stata
    //use your own username/project repo instead of the class repo below
    global repo "https://github.com/jhustata/intermediate/raw/main/"
    do ${repo}followup.do
    save followup, replace 
    import sasxport5 "https://wwwn.cdc.gov/Nchs/Nhanes/1999-2000/DEMO.XPT", clear
    merge 1:1 seqn using followup
    lookfor follow
    ```

- **Step 4) Import the self-report health assessment data**
  * Import the specific health questionnaire data:
    ```stata
    import sasxport5 "https://wwwn.cdc.gov/Nchs/Nhanes/1999-2000/HUQ.XPT", clear
    ```

- **Step 5) Analysis**
  * Save the following as project.do and upload it to you repo. Keep updating it over the next two weeks.
  ```stata
  global repo "https://github.com/jhustata/intermediate/raw/main/"
  do ${repo}followup.do
  save followup, replace 
  import sasxport5 "https://wwwn.cdc.gov/Nchs/Nhanes/1999-2000/DEMO.XPT", clear
  merge 1:1 seqn using followup
  lookfor follow
  lookfor mortstat permth_int eligstat 
  keep if eligstat==1
  capture g years=permth_int/12
  codebook mortstat
  stset years, fail(mortstat)
  sts graph, fail
  save demo_mortality, replace 
  import sasxport5 "https://wwwn.cdc.gov/Nchs/Nhanes/1999-2000/HUQ.XPT", clear 
  merge 1:1 seqn using demo_mortality, nogen
  sts graph, by(huq010) fail
  stcox i.huq010
  ```

- **Step 6) Inference**
  * Variable of interests is huq10, which is general health condition with the following categories:
       * Excellent,
       * Very good,
       * Good,
       * Fair, or
       * Poor?
       * Refused
       * Don't know
       * Missing
  * Documentation can be found [here](https://wwwn.cdc.gov/Nchs/Nhanes/1999-2000/HUQ.htm#HUQ010)
  * Run the following for inference and analysis:
    ```stata
    merge 1:1 seqn using demo_mortality, nogen
    sts graph, by(huq010) fail
    stcox i.huq010
    ```
    ```stata
    import sasxport5 "https://wwwn.cdc.gov/Nchs/Nhanes/1999-2000/HUQ.XPT", clear 
    huq010 
    desc huq010
    codebook huq010
    ```

* In week 7, we will include results and graphics from the analysis 

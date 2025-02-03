**ReadMe file for “Pre-Trends and Trade Effects of Temporary Trade Barriers” published in the Canadian Journal of Economics**

Authors: Armen Khederlarian, Sandro Steinbach 

This README file provides instructions to replicate the results presented in the aforementioned research paper using STATA. We provide do-files to replicate all results in the main article and online appendix and make available the datasets to do so. However, we only provide the do-files to create these datasets and not the raw data because for proprietary reasons the raw trade data used to construct the samples cannot be made available. We explain the steps to replicate the results and each do-file in detail below.

**Data Sources and Availability**
The analysis in this paper uses four sources of data:
1.	Global Temporary Trade Barriers (GTTB) Database by the World Bank (WB): This data is publicly available online following https://www.worldbank.org/en/data/interactive/2021/03/02/temporary-trade-barriers-database. We downloaded the data used in the analysis by 10/2022. In the replication files you can find the downloaded data in the folders “AD” for antidumping cases and “CVDs” for countervailing duties cases.
2.	U.S. imports downloaded from the U.S. census using the API.  Due to proprietary constraints, this data cannot be made available via the journal's webpage. However, we have included the do-file that allows researchers to download the data themselves. The do-file is called “download_trade_us.do”.
3.	World trade from the Global Trade Atlas (GTA) commercialized by the IHS Markit (https://www.spglobal.com/market-intelligence/en/solutions/products/maritime-global-trade-atlas). Because this data was purchased it cannot be made available via the journal's webpage. 
4.	Updated product concordance files from Pierce and Schott (2012). This raw file used to define the unique concordance between setyr and HS-10 digits can be found in “raw_files/hts_concordance_update.dta”.
   
**Organization of the Replication Files**

In the main folder of the replication files you will find all the do-files required to replicate the results.
The main folder contains the following folders:
-	do-files: Contains all do-files used to create the data and results of the main paper and online appendix. They are described below.
-	auxiliary_do_files: Do-files that are called upon by the do-files in the main folder.
-	datasets: All datasets used in the estimation and production of all results (figures and tables) of the manuscript and online appendix in stata format (.dta).
-	figures: All figures of the manuscript and online appendix in pdf format.
-	raw_files: All raw data used to create the datasets. As described above, due to proprietary constraints we cannot post the raw trade data used.
-	results: Contains the estimation results in stata format used to create all figures of the manuscript and online appendix.
-	tables: Contains the estimation results in txt format used to create all tables of the manuscript and online appendix.
-	temporary_files: This folder saves files used in the multiple steps to create the datasets used in the estimation. We have included some of the critical files to allow researchers to replicate some of the steps in creation of our datasets.

**Replication Process**
 	Here we describe how to replicate all results in the main article and online appendix, beginning with raw datasets described in “Data Sources and Availability” above. If you wish to skip the dataset construction and only replicate the estimation using our datasets (which are part of the replications files) go to step 4. If you wish to skip the estimation and only replicate the figures, go to step 5.
  
Step 0: Download U.S. Census trade data

Here we provide the two do-files we use to download the U.S. census import data between 1990 and 2018. Note users need to create their own API key and store downloaded data themselves.

“0a – download US imports.do”	
- Step 1: Request API key on API website.
-	Open https://www.census.gov/data/developers.html, click 'request a key' to get a key for API searching.
Step 2: Import country codes for each monthly import data directly from via API searching.
-	Since exported country codes each month vary, the exact list for each month should be imported from API searching in advance.
-	The country code searching mode via Census API is: https://api.census.gov/data/timeseries/intltrade/imports/hs?get=CTY_CODE&time=(type monthly date you want)&key=(type API key you requested)
Step 3: Import monthly import data directly from via API searching.
-	The searching mode via Census API is: https://api.census.gov/data/timeseries/intltrade/imports/hs?get=(type variable you want)&CTY_CODE=(type imported country code you want)&time=(type monthly date you want)
-	All variables in import dataset are listed in [Monthly Import data.xlsx, sheet("Variable")] [also search: https://api.census.gov/data/timeseries/intltrade/imports/hs/variables.html], you can also refer "list imp variables.pdf" file for list of all variables[M:/data/census/federal/].
-	Monthly data with a format of "year-month.dta" under "U.S. Merchandise of Import/Import".

“0b – format downloaded US imports.do”
-	This do-file formats the downloaded data and creates “IMP_t_m.dta” for t=[1990,2018] and m=[1,12]
-	Output: If you decide to download them you can store them in the folder “raw_files/trade_us” to enable step 2 of the the replication process.

Step 1: Create product concordances

“1a – generate concordance sample.do”	
-	This file reduces the revisions to those required by our sample period, from 1990 to 2018 and creates the product family codes (setyr) of each revision, i.e. for each group of codes that are obsolete and its new codes.
-	Input: Uses “hts_concordance_update.dta” found in raw_files, which contains all changes to the HTS code between 1990 and 2020 following the code methodology of Pierce and Schott (2012).
-	Output: “concordances_hts_1990_2018.dta”, which shall be stored in the temporary_files folder. This file contains all product code changes, that is the obsolete, the new code and the synthetic code (setyr) that identifies the same group of products. 

“1b – merge trade.do”	-	This do-file creates defines a setyr code for each HTS code of trade data. Note, as in PS12, the code still leaves some duplicates (13), i.e. HTS codes with more than one setyr. We deal with this in the next step when collapsing the trade data to the synthetic product code.
-	Input: “concordances_hts_1990_2018.dta” created by previous do-file.
-	Output: “hts_setyr_1990_2018.dta”, which shall be stored in the temporary_files folder.

Step 2: Create and merge trade and disputes data

“2a – generate US disputes.do”	
-	This do-file converts the various sheets of the excel from the GTTB database into stata format and formats some of the variables.
-	Input: “raw_files/AD” contains all antidumping investigations initiated by the U.S. from the WB’s GTTB database and was downloaded by 10/2022; “raw_files/CVDs” does the same for countervailing duties investigations. 
-	Output: “AD_USA_dataset.dta” and “CVD_USA_dataset.dta”, which are stored in temporary_files.

“2a – generate US imports.do”
-	This do-file describes how the downloaded U.S. import data is converted into the trade data we use in our analysis, at the good-country-quarter level. This includes collapsing the HTS codes into the synthetic product codes obtained by the concordances from the previous step. This creates a unique synthetic product code (setyr) for each HTS10 code (stored in “concordance_hts10.dta”).
-	Input: U.S. import data downloaded via the Census API. These files are not available in the replication files. If you decide to download them you can store them in the folder “raw_files/trade_us”, which is the directory used in this do-file.
-	Output: “imports_synth10.dta” and “concordance_hts10.dta”, which are stored in temporary_files.

“2a – generate World trade.do”
-	This do-file describes how the World trade data from the GTA of Markit IHS is converted into the trade data we use in our analysis, at the good-exporter-importer-year level. 
-	Input: World trade data from the GTA of Markit IHS. These files are not available in the replication files.
-	Output: “world_imports_`i’.dta”, for each year i ={1999,2018} which are stored in temporary_files.

“2b – merge trade & disputes data – US imports.do”
-	This do-file includes three steps: (1) it keeps the relevant information from the disputes data generated by “1a – generate US disputes.do” and formats some variables; (2) it collapses the disputes data to the good-country-initiation year level; (3) it merges the resulting disputes dataset with the U.S. imports data generated by “1a – generate US imports.do”.
-	Input: “AD_USA_dataset.dta”, “CVD_USA_dataset.dta”, “imports_synth10.dta”, “concordance_hts10.dta”
-	Output: “merged_imports_qtr_hs10.dta”

“2b – merge trade & disputes data – World trade.do”
-	This do-file does the same as the previous but using the World trade data created by “1a – generate World trade.do”. Note since this data is at HS-6 level we do not apply product concordances. An additional step in this case is to prepare the trade data before merging it with the disputes data, i.e. before step 3 of previous do-file: We only keep the 56 countries with consistently available trade data between 1999 and 2018 (listed in Table C.5 of the online Appendix); and we eliminate HS4 goods that were not targeted by any TTB case throughout our sample period, since given our fixed effects these will not affect our estimates and allow for improved computation efficiency.
-	Input: “AD_USA_dataset.dta”, “CVD_USA_dataset.dta”, “world_imports_`i’.dta” for i={1999,2018}, “cty_codes.dta”
-	Output: “dataset_full_world_yr_hs6.dta”, stored in temporary_files.

Step 3: Design estimation samples

This step is entirely performed by “3 – generate samples.do”.
The two input files are “merged_imports_qtr_hs10.dta” and “dataset_full_world_yr_hs6.dta”.
The output files are all the datasets used in the estimation of results of the manuscript and online appendix and which are then stored in the replication folder “datasets”:
1.	“dataset_base_qtr.dta”
2.	“dataset_rob1_qtr.dta”
3.	“dataset_rob2_qtr.dta”
4.	“dataset_base_qtr_rect.dta”
5.	“dataset_gt_base_qtr.dta”
6.	“dataset_world_base_yr.dta”

Step 4: Estimation

All estimations – those of results in the manuscript as well as the online appendix – are performed by “4 – estimation.do”. The file is organized by the order in which figures and tables appear in the manuscript and then in the online appendix. Inputs are the 6 datasets created in the previous steps and listed above and which are all included in the replication package.
-	To perform this estimation, the STATA command “reghdfe” needs to be installed. This can be done easily following the instructions of this link: https://scorreia.com/software/reghdfe/install.html. 
-	Most estimation procedures include loops over the three dependent variable sused: import values, quantities and unit values. Sometimes they also loop over other aspects such as fixed effects. 
-	Estimations of the extrapolated approach need to specify the period for which pre-trends are estimated. This is done in the for-loop that begins with “foreach lb in …”, where usually the value is 24, indicating we use the 24 quarters before the investigation initiation to estimate the pre-trend.
-	Estimation results are stored in the folders “results” (dta format for stata figures) and “tables” (txt format for tables).
-	Finally, note the do-file calls upon auxiliary do-files that are stored in the folder “auxiliary_do_files”.

Step 5: Plot figures

All figures can be created directly without performing the corresponding estimation using “5 – plot figures.do”. The file is organized by the order in which figures and tables appear in the manuscript and then in the online appendix. The required estimation results are included in the replication package and found in the folder “results”.

**Statement about rights**
We, the authors, certify that we have legitimate access to the data used in this manuscript and permission to use it as required. We confirm that we have the right to share, post, and redistribute the provided data included in this replication package, in accordance with all applicable data provider agreements. For proprietary data we provide the sources enabling researchers to replicate the data.

**References**
Pierce, Justin, and Peter Schott (2012) ‘Concording us harmonized system categories over time.’ Journal of Official Statistics 28(1), 53–68

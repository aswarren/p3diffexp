# Differential Expression Import Service

## Overview

The Differential Expression Import Service facilitates the upload of user-provided, pre-processed differential gene expression datasets to the user's private workspace within BV-BRC. This service supports datasets generated from various technologies, including microarray, RNA-Seq, and proteomics. The uploaded datasets can then be analyzed and compared with other expression datasets available in BV-BRC using the platform's annotation and analysis tools. 

**Key Points:**

*   The service is designed for importing pre-processed differential expression data, meaning users should provide data that has already undergone normalization and differential expression analysis.
*   **Currently, BV-BRC only supports differential gene expression data in the form of log ratios**. These log ratios should represent the fold-change in expression between two conditions, time points, or samples.

## About This Module

This module is a component of the BV-BRC build system and is designed to integrate seamlessly with the `dev_container` infrastructure, which manages the development and production deployment of BV-BRC components. More documentation on the `dev_container` infrastructure is available [here](https://github.com/BV-BRC/dev_container/tree/master/README.md). 

This module provides the following application specification:

*   [DifferentialExpressionImport](app_specs/DifferentialExpressionImport.md)

## Service Input

The Differential Expression Import Service requires two input files:

**1. Differential Expression Data File:**

*   This file should contain a table of differential gene expression data, where:
    *   Rows represent genes or features.
    *   Columns represent comparisons between conditions, time points, or samples.
    *   Values represent log ratios (e.g., log2 fold change) indicating the differential expression between the two compared entities. 
*   The file format should be either a tab-delimited text file (.txt) or a comma-separated value file (.csv).
*   The first row should contain headers that clearly label each comparison.
*   The first column should contain unique identifiers for each gene or feature.  These identifiers should be compatible with the BV-BRC annotation system (e.g., PATRIC feature IDs, RefSeq IDs). 

**2. Metadata File:**

*   This file provides essential information about the experimental design and data processing, allowing BV-BRC to properly interpret and contextualize the uploaded dataset. The file should be in JSON format and include the following information:
    *   **Experimental Conditions:** A list of the experimental conditions, time points, or samples compared in the dataset.
    *   **Comparisons:** A list defining the specific pairwise comparisons made, corresponding to the columns in the data file.
    *   **Normalization Method:** The normalization method used to pre-process the raw expression data.
    *   **Differential Expression Method:** The statistical method used to calculate the differential expression values (e.g., DESeq2, edgeR, limma).

## Service Output

Upon successful upload, the Differential Expression Import Service creates a new differential expression dataset in the user's private workspace. This dataset can be accessed and analyzed using various BV-BRC tools, including:

*   **Expression Data Viewer:**  Visualize the expression profiles of individual genes or sets of genes across different comparisons.
*   **Comparative Pathway Viewer:**  Analyze the differential expression of genes within specific metabolic pathways.
*   **Similar Genome Finder:**  Identify genomes with similar expression profiles.

## Scripts and Utilities

The following Python script plays a key role in processing and transforming the uploaded differential expression data:

| Script Name                                             | Purpose                                                                                                                                                                                                                                                                        |
| ------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [expression_transform.py](expression_transform.py) | This script performs several essential tasks: <br> * **Data validation:** It checks the format and content of the uploaded data and metadata files to ensure consistency and correctness. <br> * **Data transformation:** It converts the input data into a format suitable for storage and analysis within the BV-BRC platform. <br> * **Metadata integration:** It combines the uploaded metadata with additional information from BV-BRC, such as gene annotations and pathway information, to provide a comprehensive context for the dataset. |


usage: expression_transform.py [-h] (--ufile UFILE | --ustring USTRING)
                               (--sfile SFILE | --sstring SSTRING)

optional arguments:
  -h, --help         show this help message and exit
  --ufile UFILE      json file from user input
  --ustring USTRING  json string from user input
  --sfile SFILE      server setup JSON file
  --sstring SSTRING  server setup JSON string

#To run locally likely need __init__.py in the lib folder. Currently missing to avoid conflicts on server.

#Input
#1. metadata in json with the following:

"""
{xfile:"comparisons file",
xformat:"csv || tsv || xls ||  xlsx",
xsetup:"gene_matrix || gene_list",
source_id_type:"refseq_locus_tag || alt_locus_tag || feature_id",
data_type: "Transcriptomics || Proteomics || Phenomics",
title: "User input",
description: "User input",
organism: "user input",
pmid: "user_input",
output_path: "path",
"metadata_template":"file",
"metadata_format":"csv || tsv || xls ||  xlsx"}
"""
#2. server info for the data api
"""
{"data_api":"url"}
"""

#Sample Output
#experiment.json
#{"origFileName":"filename","geneMapped":4886,"samples":8,"geneTotal":4985,"cdate":"2013-01-28 13:40:47","desc":"user input","organism":"some org","owner":"user name","title":"user input","pmid":"user input","expid":"whatever","collectionType":"ExpressionExperiment","genesMissed":99,"mdate":"2013-01-28 13:40:47"}

#expression.json
#{"expression":[{"log_ratio":"0.912","na_feature_id":"36731006","exp_locus_tag":"VBISalEnt101322_0001","pid":"8f2e7338-9f04-4ba5-9fe2-5365c857d57fS0","z_score":"-0.23331085637221843"}]

#mapping.json
#{"mapping":{"unmapped_list":[{"exp_locus_tag":"VBISalEnt101322_pg001"}],"unmapped_ids":99,"mapped_list":[{"na_feature_id":"36731006","exp_locus_tag":"VBISalEnt101322_0001"}],"mapped_ids":4886}}

#sample.json
#{"sample":[{"sig_log_ratio":2675,"expmean":"1.258","sampleUserGivenId":"LB_stat_AerobicM9_stat_aerobic","expname":"LB_stat_AerobicM9_stat_aerobic","pid":"8f2e7338-9f04-4ba5-9fe2-5365c857d57fS0","genes":4429,"sig_z_score":139,"expstddev":"1.483"}]}

## See Also

*   [Differential Expression Import Service](https://bv-brc.org/app/DifferentialExpressionImport)
*   [Differential Expression Import Service Tutorial](https://www.bv-brc.org/docs/tutorial/expression_import/expression_import.html)



# Saving ESG Bulk Content as CSV Files

## Introduction

ESG stands for Environmental Social and Governance (ESG) content.  This extra-financial information enables investment processes to identify companies with
quality management and reduced risk exposure.

Refinitiv makes ESG available as part of Refinitiv Data PLatform (RDP) that provides simple web based API access to a broad range of content.

Bulk ESG data is scores and measures in efficient weekly bulk files.

In this article we are going to discuss a common use case:

How to process multi-hierarchical, representational JSON ESG Bulk files into flat CSV files that are required by processing by some applications, and preferred for review by a significant 

## Pre-requisites

Files that we use are available on disk. To learn more of how to requesta and download ESG Bulk content please refer to
https://developers.refinitiv.com/en/article-catalog/article/how-to-identify-and-request-esg-bulk-content---python

## Flatten ESG Scores File

This is quick to run and very simple in terms of processing required:
```
fileNameRoot = 'RFT-ESG-Scores-Current-init-2021-05-02'

filedestinationpath = '.\\'
filename = filedestinationpath + fileNameRoot + '.jsonl.gz'
f=gzip.open(filename,'rb')
file_content=f.read()
lines = file_content.splitlines()
df_inter = pd.DataFrame(lines)
df_inter.columns = ['json_element']
df_resolve = df_inter['json_element'].apply(json.loads)
df_resolve
df_final = pd.json_normalize(df_resolve)
df_final['ESGOrganization.Names.Name.OrganizationName'] = pd.json_normalize(df_final['ESGOrganization.Names.Name.OrganizationName'].str[0])
resultspth = filedestinationpath + fileNameRoot + '.csv'
df_final.to_csv(resultspth, index = False)
df_final
```
Resulting in
![alt text](https://github.com/zfarberov/zfarberov.github.io/tree/main/ESGBulkAsCSV/)
## Flatten ESG Symbology SEDOL

Symbology SEDOL carries multiple Quotes as nexted objects, this takes longer to process..

# nf-core/fetchngs

**Running nf-core/fetchngs on GCP**


1- Get in your VM on GCP 

2- Create top level nextflow.config `nano  nextflow.config`


```
# paste following accroding your location of conf files
profiles {
  batch            { includeConfig 'conf/test-batch.config' }
}
```


3- Create custom batch config file `nano conf/test-batch.config`. I have mounted GCS bucket as working directory here.

```
params {
    config_profile_name        = 'AS Google Batch profile'
    config_profile_description = 'AS Google Batch Configuration'
}

workDir = 'gs://pmodalpha/workdir3'

process {
    executor = 'google-batch'

}
google {
     project = 'your GCP project id which is alpha numeric'
     location = 'us-central1'
}

```

4- Run on test dataset. I mounted GCS bucket as outdir here.

```
echo PRJNA940201 > ids.csv

# 15 samples | unstranded | Paired end  

nextflow run nf-core/fetchngs \
-r 1.11.0 \
--input ids.csv \
--input_type sra \
-profile batch,docker 
--outdir gs://path/to/your/gcsbucket
```


These warnings may keep on appearing but need to fix it.
```
WARN: Batch job cannot be run: VM in Managed Instance Group meets error: Batch Error: code - CODE_GCE_QUOTA_EXCEEDED, description - error count is 4, latest message example: Instance 'nf-be93a727-170767-bb09690d-ae36-4f790-group0-0-5pdz' creation failed: Quota 'N2_CPUS' exceeded.  Limit: 24.0 in region us-central1
```

Output looks like this.

![image](https://github.com/amnahsiddiqa/cloud_nfcore_pipelines/assets/28387956/2a80528c-1fa6-466a-b693-7149bc484c21)




## Run on Seqera

- git add github_pat
See more on https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens
```
datasets:
  - name: 'fetchngstest'
    description: 'testfetchngs'
    header: false
    workspace: 'numeric' 
    file-path: 'ids.csv'
    overwrite: True

  ## To see the full list of options available run: "tw pipelines add"
## The options required to create pipelines can be specified:
##   1. Explicitly in this file
##   2. Via a JSON file exported from SeqeraPlatform with the "tw pipelines export" command
pipelines:
  - name: 'my_first_fetchngs'                         # required
    url: "https://github.com/amnahsiddiqa/fetchngs"
    workspace: 'numeric'         # required
    description: 'My test pipeline'                   # optional
    compute-env: 'hipciiigv'         # required
    work-dir: 'gs://rnavarhipciii'                        # optional                                  # optional
    revision: 'master'                                  # required
    params:                                           # optional
      outdir: 'gs://rnavarhipciii/test'
    config: './nextflow.config'          # optional
      
  
```

## To do 

- Add config file to control resources to deal with quotas etc by using forks and error strategies.
- How to delete all datsets at once from seqera

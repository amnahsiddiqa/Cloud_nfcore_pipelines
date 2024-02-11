# nf-core/fetchngs

**Running nf-core/fetchngs on GCP**


1- Get in your VM on GCP 
2- create top level nextflow.config `nano  nextflow.config`


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
It is  

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


## To do 

- Add config file to control resources to deal with quotas etc by using forks and error strategies.

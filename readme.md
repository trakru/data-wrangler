# Connecting to S3 buckets/services from a local computer 

Describing the process to access AWS resources by using environment variables. Please dont use your account credentials in cleartext when trying to connect to AWS - thats a potential security nightmare and most of you have very high-level privileges with AWS. 

Basic process is as follows:
1. generate credentials via the saml auth script
2. how to use the `aws --profile` flag and/or other options for connection 


### Step 1: Generating Credentials

Be sure to run samlapi_formauth.py in order to create the credentials file. Install Process is as follows:

1. Install the AWS CLI:
https://docs.aws.amazon.com/cli/latest/userguide/installing.html

2. Stub out a basic ~/.aws/credentials file. If you already have a ~/.aws/credentials, rename it to credentials.backup

>`[default]
>output = json
>region = us-east-2
>aws_access_key_id = 
>aws_secret_access_key = `


3. Execute `samlapi_formauth.py` from shell of your choice 


4. Provide your Commscope credentials and select the desired account/role (for people with dual roles). People with single roles (i.i. Account Owner only) will not get any prompts. 

5. Your new access key pair has been stored in the AWS configuration file /Users/`yourusername`/.aws/credentials under the saml profile.

### Step 2: Using the generated credentials programmatically

there are multiple flag options available, but basically, it boils down to letting the service know that all credentials should be directed to `saml` profile


```python
import awswrangler as wr
import pandas as pd
from datetime import datetime
import boto3
```


```python
# Option 1: use a setup_default_session scenario

boto3.setup_default_session(profile_name='saml')
wr.s3.does_object_exist("s3://noaa-ghcn-pds/fake") # Should return false
```




    False




```python
# Testing with a small csv in our s3 bucket

df = wr.s3.read_csv(f"s3://xwifi-od-s3-transform/converted-data/gis/final/gis-master_snmpMAC_updated.csv")
```


```python
df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>apMAC</th>
      <th>Model</th>
      <th>Street</th>
      <th>Street2</th>
      <th>City</th>
      <th>State</th>
      <th>Zipcode</th>
      <th>Latitude</th>
      <th>Longitude</th>
      <th>Region</th>
      <th>Zone</th>
      <th>zoneType</th>
      <th>SiteID</th>
      <th>type</th>
      <th>geometry</th>
      <th>snmpReportedMac</th>
      <th>apTZ</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>3c:7a:8a:84:1e:fc</td>
      <td>NaN</td>
      <td>1305 KATER ST</td>
      <td>NaN</td>
      <td>PHILADELPHIA</td>
      <td>PA</td>
      <td>19147</td>
      <td>39.943095</td>
      <td>-75.163699</td>
      <td>Freedom</td>
      <td>South St Zone 1</td>
      <td>Secondary</td>
      <td>NaN</td>
      <td>home_hotspot</td>
      <td>POINT (-75.16369899999999 39.943095)</td>
      <td>3c:7a:8a:84:1e:fd</td>
      <td>America/New_York</td>
    </tr>
    <tr>
      <th>1</th>
      <td>5c:8f:e0:b1:18:02</td>
      <td>NaN</td>
      <td>1110 N HOPE ST</td>
      <td>NaN</td>
      <td>PHILADELPHIA</td>
      <td>PA</td>
      <td>19123</td>
      <td>39.967898</td>
      <td>-75.137222</td>
      <td>Freedom</td>
      <td>Fishtown</td>
      <td>Secondary</td>
      <td>NaN</td>
      <td>home_hotspot</td>
      <td>POINT (-75.13722199999999 39.967898)</td>
      <td>5c:8f:e0:b1:18:03</td>
      <td>America/New_York</td>
    </tr>
    <tr>
      <th>2</th>
      <td>00:1d:d5:bc:e9:74</td>
      <td>NaN</td>
      <td>631 S 9TH ST</td>
      <td>NaN</td>
      <td>PHILADELPHIA</td>
      <td>PA</td>
      <td>19147</td>
      <td>39.941791</td>
      <td>-75.157001</td>
      <td>Freedom</td>
      <td>South St Zone 1</td>
      <td>Secondary</td>
      <td>NaN</td>
      <td>home_hotspot</td>
      <td>POINT (-75.15700099999999 39.941791)</td>
      <td>00:1d:d5:bc:e9:75</td>
      <td>America/New_York</td>
    </tr>
    <tr>
      <th>3</th>
      <td>9c:34:26:71:66:d8</td>
      <td>NaN</td>
      <td>833 BAINBRIDGE ST</td>
      <td>NaN</td>
      <td>PHILADELPHIA</td>
      <td>PA</td>
      <td>19147</td>
      <td>39.941846</td>
      <td>-75.156767</td>
      <td>Freedom</td>
      <td>South St Zone 1</td>
      <td>Secondary</td>
      <td>NaN</td>
      <td>home_hotspot</td>
      <td>POINT (-75.156767 39.941846)</td>
      <td>9c:34:26:71:66:d9</td>
      <td>America/New_York</td>
    </tr>
    <tr>
      <th>4</th>
      <td>78:23:ae:f2:47:77</td>
      <td>NaN</td>
      <td>1137 PINE ST</td>
      <td>APT 103</td>
      <td>PHILADELPHIA</td>
      <td>PA</td>
      <td>19107</td>
      <td>39.945278</td>
      <td>-75.160919</td>
      <td>Freedom</td>
      <td>South St Zone 1</td>
      <td>Secondary</td>
      <td>NaN</td>
      <td>home_hotspot</td>
      <td>POINT (-75.16091900000001 39.945278)</td>
      <td>78:23:ae:f2:47:78</td>
      <td>America/New_York</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Option 2: define the session individually with boto

session = boto3.session.Session(profile_name='saml')
s3 = session.resource('s3')
```


```python
for bucket in s3.buckets.all():
    print(bucket.name)
```

    aws-emr-resources-951365680536-us-east-2
    aws-glue-scripts-951365680536-us-east-2
    aws-glue-temporary-951365680536-us-east-2
    aws-logs-951365680536-us-east-2
    cf-templates-asdrdk4p9flh-us-east-2
    dataenglab-databucket-11zhm3zwg60yu
    dataenglab-scriptbucket-19ecl0o6eqei7
    dms-wscaavgwoyx3v3gk3sf42tr2n6i2qj6v7k52xqa
    redshift-xfinity-preimport
    sagemaker-studio-fey3c4xpe2g
    sagemaker-us-east-2-951365680536
    terraform-dev-bucket-ankush
    xwifi-adhoc-reports
    xwifi-od-s3-parquet
    xwifi-od-s3-testingbkt
    xwifi-od-s3-transform
    xwifi-redshift-clientapp-sns
    

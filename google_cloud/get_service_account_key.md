# **Get a Google Cloud service account key json file**

How to generate the json file that can be used to authenticate in the python api (BigQuery etc.)



<br><br>


---
**Contents**
- [**Get a Google Cloud service account key json file**](#get-a-google-cloud-service-account-key-json-file)
  - [**Register to Google Cloud**](#register-to-google-cloud)
  - [**Create a new project**](#create-a-new-project)
  - [**Create a new service account and get the key**](#create-a-new-service-account-and-get-the-key)


---

## **Register to Google Cloud**

- from `https://cloud.google.com/`, go to `console`

## **Create a new project**
- Go to the the project dropdown menu at the top left next to "Google Cloud"  
- Select "New Project" at the top right corner, fill necessary informations

## **Create a new service account and get the key**

- Go to "Service accounts" on the left menun and `create a service account`  at the top

- Enter a service name, and click `create and continue`

- In the "Service Account Permissions" section, specify the roles and permissions for the service account (ex for BigQuery, select the "BigQuery Admin" role)   

- Click "Continue"  

- Click "Grant users access to this service account" section, you can leave it empty for now and click "Done"

- Back to the service accounts list, find the new service account, click on the three dots icon in the "Actions" column

- In the "Keys" section, click on the "Add Key" dropdown and select "Create new key".

- In the "Create private key" dialog, select "JSON" as the key type and click "Create".

- The JSON key file will be downloaded to your computer. Save it in a secure location.

- Set the path to the downloaded JSON key file in the GOOGLE_APPLICATION_CREDENTIALS environment variable. You can do this by running the following command in your terminal or adding it to your environment variables:


```bash
export GOOGLE_APPLICATION_CREDENTIALS="/path/to/your/service-account-key.json"
```



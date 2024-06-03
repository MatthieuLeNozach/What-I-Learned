# **Enable BigQuery API**

Enable the BigQuery API in the Google Cloud Console to use BigQuery from the Python API.

<br><br>

---

**Contents**

- [**Enable BigQuery API**](#enable-bigquery-api)
  - [**Enable BigQuery API in the Cloud Console**](#enable-bigquery-api-in-the-cloud-console)
  - [**Using BigQuery from Python**](#using-bigquery-from-python)

---

## **Enable BigQuery API in the Cloud Console**

To enable the BigQuery API in the Google Cloud Console:

- Go to the [Google Cloud Console](https://console.cloud.google.com/) and select the project
- Select "APIs & Services" > "Library"
- Search for "BigQuery API"
- Click the "Enable" button


## **Using BigQuery from Python**

```python
pip install google-cloud-bigquery
```
Export the path to the `json` file:
```bash
export GOOGLE_APPLICATION_CREDENTIALS="/path/to/your/service-account-key.json"
```

In the code, instantiate a BigQuery object:
```py
from google.cloud import bigquery

client = bigquery.Client()
...
```


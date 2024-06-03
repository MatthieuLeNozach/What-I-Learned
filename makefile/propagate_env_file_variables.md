# **Propagate env variables from the `.env` file to the make commands**


Some brief description



<br><br>


---
**Contents**
- [**Propagate env variables from the `.env` file to the make commands**](#propagate-env-variables-from-the-env-file-to-the-make-commands)
  - [**Create a `.env` file**](#create-a-env-file)
  - [**Start the `Makefile` with an export command of the whole `.env` file**](#start-the-makefile-with-an-export-command-of-the-whole-env-file)
  - [**Refer to the env variables in the make commands**](#refer-to-the-env-variables-in-the-make-commands)


---

## **Create a `.env` file**

example:
```ini
S3_PATH=s3://my-s3-bucket # output s3 path
AWS_PROFILE=default # aws profile to use
GCP_PROJECT=my-gcp-project # GCP project to use
GOOGLE_APPLICATION_CREDENTIALS=/path/to/credentials/jsonfile.json
TRANSFORM_S3_PATH_INPUT=s3://my-input-bucket/pypi_file_downloads/*/*/*.parquet # For transform pipeline, input source data
TRANSFORM_S3_PATH_OUTPUT=s3://my-output-bucket/ # For transform pipeline, output source if putting data to s3
```

## **Start the `Makefile` with an export command of the whole `.env` file**

```makefile

include .env
export
...
```

## **Refer to the env variables in the make commands**

Use `$$` to refer to the exported variables

example with a `test-bigquery-connection`
```makefile
test-bigquery-connection:
    poetry run python3 -m test_gcp_bigquery.py \
        --gcp-credentials $$GOOGLE_APPLICATION_CREDENTIALS
```
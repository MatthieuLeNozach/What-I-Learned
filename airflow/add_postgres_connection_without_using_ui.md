# **Add a Postgres connection without using Airflow UI**



From the Airflow docs:
> Airflow connections may be defined in environment variables.
> 
> The naming convention is [**`AIRFLOW_CONN_{CONN_ID}`**](https://airflow.apache.org/docs/apache-airflow/stable/cli-and-env-variables-ref.html#envvar-AIRFLOW_CONN_-CONN_ID), all uppercase (note the single underscores surrounding `CONN`). So if your connection id is `my_prod_db` then the variable name should be `AIRFLOW_CONN_MY_PROD_DB`.
> 
> The value can be either JSON or Airflow’s URI format.
> 

<br><br>


---
**Contents**
- [**Add a Postgres connection without using Airflow UI**](#add-a-postgres-connection-without-using-airflow-ui)
  - [**Create the env variable**](#create-the-env-variable)
  - [**Referencing it in the DAGs**](#referencing-it-in-the-dags)


---

## **Create the env variable**

In the `.env` file, add a new connection URI with the env variable naming convention
`AIRFLOW_CONN_{CONN_ID}`
The value of this env variable is in the format
`postgresql+psycopg2://{USER}:{PASSWORD}@{HOST}:{PORT}/{DB}`  

Example:
```ini
AIRFLOW_CONN_VELIB_POSTGRES=postgresql+psycopg2://velib_user:velib_password@user-postgres:5432/velib
```


## **Referencing it in the DAGs**

At the beginning of the file, import the PostgresHook and instantiate the hook:

```py
from airflow.hooks.postgres_hook import PostgresHook
...

pg_hook = PostgresHook(postgres_conn_id='velib_postgres')
```

Finally, use the PostgresHook methods in the tasks:
```py

pg_hook.get_conn()
...
pg_hook.run(insert_query, parameters=record)
...
pg_hook.get_first("SELECT COUNT(*) FROM locations")

```
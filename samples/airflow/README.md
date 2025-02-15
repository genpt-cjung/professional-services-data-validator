# Data Validation on Airflow (ideally Cloud Composer)

DVT on Airflow makes use of our [latest released version of DVT library on PyPi](https://pypi.org/project/google-pso-data-validator/#history).
There is no specific operator for it at the moment, so the DAG uses the
main structures of the library itself.

The input is a JSON data representation of the DVT run configuration. 

By default, the DAG will output the results to BigQuery as a result handler.

### Requirements to run the DAG:
- Pre-existing Airflow environment created with Public IP (Private environment disabled)
- Create an Airflow variable called `gcp_project` with the GCP Project ID

### Instructions

1. Download the DAG file in this directory
2. Update the JSON configuration for your use case (update table names, etc.)
3. Upload it to the DAGs folder in your Airflow environment

### Limitations 

The Airflow DAG expects the raw config JSON which is not the same as a YAML config converted to JSON.

Parameters in a typical YAML config file are slightly different from the raw JSON config, 
which is generated after DVT parses the YAML. The [build_config_manager()](https://github.com/GoogleCloudPlatform/professional-services-data-validator/blob/develop/data_validation/config_manager.py#L429) 
method generates the JSON config and should be used as a reference.

Our Cloud Run sample also expects a raw JSON config in the `'data'` variable shown
[here](https://github.com/GoogleCloudPlatform/professional-services-data-validator/tree/develop/samples/run#test-cloud-run-endpoint).

For example, the following YAML config is equivalent to the JSON config below, where the source param is written as `source_conn`.

```yaml
result_handler: {}
source: my_bq_conn
target: my_bq_conn
validations:
- aggregates:
  - field_alias: count
    source_column: null
    target_column: null
    type: count
...
```

```json5
{
    "source_conn": BQ_CONN,
    "target_conn": BQ_CONN,
    "aggregates": [
        {
            "field_alias": "count",
            "source_column": None,
            "target_column": None,
            "type": "count"
        }
    ]
}
```

For more implementation details, [this](https://github.com/GoogleCloudPlatform/professional-services-data-validator/blob/develop/data_validation/config_manager.py#L444) 
is where the raw JSON config is generated in the DVT code.

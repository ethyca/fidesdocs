
# Rollbar

## Implementation Summary

Fides uses the following Rollbar endpoints to retrieve and delete Personally Identifiable Information (PII) when processing a Privacy Request. Right to Access and Right to Delete (Right to Forget) support for each endpoint is noted below.

|Endpoint | Right to Access | Right to Delete |
|----|----|----|
|[Projects](https://docs.rollbar.com/reference/get-a-project) | Yes | No |
|[Instances](https://docs.rollbar.com/reference/get_api-1-instances) | Yes | No |
|[People](https://docs.rollbar.com/reference/delete-a-person) | Yes | Yes |


## Connection Settings

Connection instructions may be found in the [configuration guide](../saas_config).

**Deletion requests** are fulfilled by masking PII via `UPDATE` endpoints. To [give Fides permission](../../../installation/configuration#configuration-variable-reference) to remove PII using `DELETE` endpoints, ensure the `masking_strict` variable in your `fides.toml` file is set to `false`.

## Example Rollbar Configuration

```yaml
saas_config:
  fides_key: <instance_fides_key>
  name: Rollbar SaaS Config
  type: rollbar
  description: A sample schema representing the Rollbar connector for Fides
  version: 0.0.1

  connector_params:
    - name: domain
    - name: read_access_token
    - name: write_access_token
    - name: page_limit
      default_value: 100
      description: The maximum number of pages to return from Rollbar's "instances" endpoint

  client_config:
    protocol: https
    host: <domain>

  test_request:
    method: GET
    path: /api/1/status/ping

  endpoints:
    - name: projects
      requests:
        read:
          method: GET
          path: /api/1/projects
          headers:
            - name: X-Rollbar-Access-Token
              value: <read_access_token>
          param_values:
            - name: read_access_token
              connector_param: read_access_token
            - name: placeholder
              identity: email
          data_path: result
    - name: project_access_tokens
      requests:
        read:
          method: GET
          path: /api/1/project/<project_id>/access_tokens
          headers:
            - name: X-Rollbar-Access-Token
              value: <read_access_token>
          param_values:
            - name: read_access_token
              connector_param: read_access_token
            - name: project_id
              references:
                - dataset: <instance_fides_key>
                  field: projects.id
                  direction: from
          postprocessors:
            - strategy: filter
              configuration:
                field: name
                value: read
          data_path: result
    - name: instances
      requests:
        read:
          method: GET
          path: /api/1/instances
          headers:
            - name: X-Rollbar-Access-Token
              value: <project_read_token>
          query_params:
            - name: page
              value: 1
          param_values:
            - name: project_read_token
              references:
                - dataset: <instance_fides_key>
                  field: project_access_tokens.access_token
                  direction: from
          pagination:
            strategy: offset
            configuration:
              incremental_param: page
              increment_by: 1
              limit:
                connector_param: page_limit
          postprocessors:
            - strategy: filter
              configuration:
                field: data.person.email
                value:
                  identity: email
          data_path: result.instances
    - name: people
      requests:
        delete:
          method: POST
          path: /api/1/people/delete_jobs
          query_params:
            - name: email
              value: <email>
          headers:
            - name: X-Rollbar-Access-Token
              value: <write_access_token>
          param_values:
            - name: write_access_token
              connector_param: write_access_token
            - name: email
              identity: email
```

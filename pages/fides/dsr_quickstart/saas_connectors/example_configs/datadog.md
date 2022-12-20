
# Datadog

## Implementation Summary

Fides uses the following Datadog endpoints to retrieve and delete Personally Identifiable Information (PII) when processing a Privacy Request. Right to Access and Right to Delete (Right to Forget) support for each endpoint is noted below.

|Endpoint | Right to Access | Right to Delete |
|----|----|----|
|[Events](https://docs.datadoghq.com/api/latest/logs/#get-a-list-of-logs) | Yes | No |

## Connection Settings

Connection instructions may be found in the [configuration guide](../saas_config).

**Deletion requests** are fulfilled by masking PII via `UPDATE` endpoints. To [give Fides permission](../../../installation/configuration#configuration-variable-reference) to remove PII using `DELETE` endpoints, ensure the `masking_strict` variable in your `fides.toml` file is set to `false`.

## Example Datadog Configuration

```yaml
saas_config:
  fides_key: <instance_fides_key>
  name: Datadog SaaS Config
  type: datadog
  description: A sample schema representing the Datadog connector for Fides
  version: 0.0.2

  connector_params:
    - name: domain
      default_value: api.datadoghq.com
    - name: api_key
    - name: app_key

  client_config:
    protocol: https
    host: <domain>
    authentication:
      strategy: api_key
      configuration:
        headers:
          - name: DD-APPLICATION-KEY
            value: <app_key>
          - name: DD-API-KEY
            value: <api_key>

  test_request:
    method: GET
    path: /api/v2/logs/events

  endpoints:
    - name: events
      requests:
        read:
          - method: GET
            path: /api/v2/logs/events
            query_params:
              - name: filter[query]
                value: <email>
              - name: filter[from]
                value: 0
              - name: filter[to]
                value: now
              - name: page[limit]
                value: 1000
            param_values:
              - name: email
                identity: email
            data_path: data
            pagination:
              strategy: link
              configuration:
                source: body
                path: links.next
          - method: GET
            path: /api/v2/logs/events
            query_params:
              - name: filter[query]
                value: <phone_number>
              - name: filter[from]
                value: 0
              - name: filter[to]
                value: now
              - name: page[limit]
                value: 1000
            param_values:
              - name: phone_number
                identity: phone_number
            data_path: data
            pagination:
              strategy: link
              configuration:
                source: body
                path: links.next
```


# DoorDash

## Implementation Summary

Fides uses the following DoorDash endpoints to retrieve and delete Personally Identifiable Information (PII) when processing a Privacy Request. Right to Access and Right to Delete (Right to Forget) support for each endpoint is noted below.

|Endpoint | Right to Access | Right to Delete |
|----|----|----|
|[Deliveries](https://developer.doordash.com/en-US/api/drive#tag/Delivery/operation/GetDelivery) | Yes | No |

## Connection Settings

Connection instructions may be found in the [configuration guide](../saas_config).

**Deletion requests** are fulfilled by masking PII via `UPDATE` endpoints. To [give Fides permission](../../../installation/configuration#configuration-variable-reference) to remove PII using `DELETE` endpoints, ensure the `masking_strict` variable in your `fides.toml` file is set to `false`.

## Example DoorDash Configuration

```yaml
saas_config:
  fides_key: <instance_fides_key>
  name: Doordash SaaS Config
  type: doordash
  description: A sample schema representing the Doordash connector for Fides
  version: 0.0.1

  connector_params:
    - name: domain
      default_value: openapi.doordash.com
    - name: developer_id
      label: Developer ID
    - name: key_id
      label: Key ID
    - name: signing_secret

  external_references:
    - name: doordash_delivery_id
      label: Doordash Delivery ID

  client_config:
    protocol: https
    host: <domain>
    authentication:
      strategy: doordash
      configuration:
        developer_id: <developer_id>
        key_id: <key_id>
        signing_secret: <signing_secret>

  test_request:
    method: GET
    path: /developer/v1/businesses

  endpoints:
    - name: deliveries
      requests:
        read:
          method: GET
          path: /drive/v2/deliveries/<delivery_id>
          param_values:
            - name: delivery_id
              references:
                - doordash_delivery_id
```

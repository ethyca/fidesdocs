
# Fullstory

## Implementation Summary

Fides uses the following Fullstory endpoints to retrieve and delete Personally Identifiable Information (PII) when processing a Privacy Request. Right to Access and Right to Delete (Right to Forget) support for each endpoint is noted below.

|Endpoint | Right to Access | Right to Delete |
|----|----|----|
|[Users](https://developer.fullstory.com/get-user) | Yes | Yes |

## Connection Settings

Connection instructions may be found in the [configuration guide](../saas_config).

**Deletion requests** are fulfilled by masking PII via `UPDATE` endpoints. To [give Fides permission](../../../installation/configuration#configuration-variable-reference) to remove PII using `DELETE` endpoints, ensure the `masking_strict` variable in your `fides.toml` file is set to `false`.

## Example FullStory Configuration

```yaml
saas_config:
  fides_key: <instance_fides_key>
  name: Fullstory SaaS Config
  type: fullstory
  description: A sample schema representing the Fullstory connector for Fides
  version: 0.0.1

  connector_params:
    - name: domain
      default_value: api.fullstory.com
    - name: api_key

  external_references:
    - name: fullstory_user_id
      label: Fullstory User ID
      description: Dataset reference to the location of Fullstory user IDs

  client_config:
    protocol: https
    host: <domain>
    authentication:
      strategy: api_key
      configuration:
        headers:
          - name: Authorization
            value: Basic <api_key>

  test_request:
    method: GET
    path: /operations/v1

  endpoints:
    - name: user
      requests:
        read:
          method: GET
          path: /users/v1/individual/<user_id>
          param_values:
            - name: user_id
              references:
                - fullstory_user_id
        update:
          method: POST
          path: /users/v1/individual/<user_id>/customvars
          body: |
            {
              <masked_object_fields>
            }
          param_values:
            - name: user_id
              references:
                - dataset: <instance_fides_key>
                  field: user.uid
                  direction: from
```

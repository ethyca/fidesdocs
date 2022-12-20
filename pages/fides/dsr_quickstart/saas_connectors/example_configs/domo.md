
# Domo

## Implementation Summary

Fides uses the following Domo endpoints to retrieve and delete Personally Identifiable Information (PII) when processing a Privacy Request. Right to Access and Right to Delete (Right to Forget) support for each endpoint is noted below.

|Endpoint | Right to Access | Right to Delete |
|----|----|----|
|[Users](https://developer.domo.com/docs/users-api-reference/users-2#List%20users) | Yes | No |

## Connection Settings

Connection instructions may be found in the [configuration guide](../saas_config).

**Deletion requests** are fulfilled by masking PII via `UPDATE` endpoints. To [give Fides permission](../../../installation/configuration#configuration-variable-reference) to remove PII using `DELETE` endpoints, ensure the `masking_strict` variable in your `fides.toml` file is set to `false`.

## Example Domo Configuration

```yaml
saas_config:
  fides_key: <instance_fides_key>
  name: Domo SaaS Config
  type: domo
  description: A sample schema representing the Domo connector for Fides
  version: 0.0.1

  connector_params:
    - name: domain
    - name: client_id
    - name: client_secret

  client_config:
    protocol: https
    host: <domain>
    authentication:
      strategy: oauth2_client_credentials
      configuration:
        token_request:
          method: POST
          path: /oauth/token
          headers:
            - name: Content-Type
              value: application/x-www-form-urlencoded
          client_config:
            protocol: https
            host: <domain>
            authentication:
              strategy: basic
              configuration:
                username: <client_id>
                password: <client_secret>
          body: |
            {
              "grant_type": "client_credentials"
            }

  test_request:
    method: GET
    path: /v1/datasets

  endpoints:
    - name: user
      requests:
        read:
          method: GET
          path: /v1/users
          query_params:
            - name: limit
              value: 500
            - name: offset
              value: 0
          param_values:
            - name: placeholder
              identity: email
          pagination:
            strategy: offset
            configuration:
              incremental_param: offset
              increment_by: 500
          postprocessors:
            - strategy: filter
              configuration:
                field: email
                value:
                  identity: email
        update:
          request_override: domo_user_update
          param_values:
            - name: user_id
              references:
                - dataset: <instance_fides_key>
                  field: user.id
                  direction: from
```

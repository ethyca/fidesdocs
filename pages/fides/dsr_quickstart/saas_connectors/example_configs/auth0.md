
# Auth0

## Implementation Summary

Fides uses the following Auth0 endpoints to retrieve and delete Personally Identifiable Information (PII) when processing a Privacy Request. Right to Access and Right to Delete (Right to Forget) support for each endpoint is noted below.

|Endpoint | Right to Access | Right to Delete |
|----|----|----|
|[Users](https://auth0.com/docs/manage-users/user-search/retrieve-users-with-get-users-endpoint) | Yes | Yes |
|[Logs](https://auth0.com/docs/deploy-monitor/logs/retrieve-log-events-using-mgmt-api) | Yes | No |

## Connection Settings

Connection instructions may be found in the [configuration guide](../saas_config).

**Deletion requests** are fulfilled by masking PII via `UPDATE` endpoints. To [give Fides permission](../../../installation/configuration#configuration-variable-reference) to remove PII using `DELETE` endpoints, ensure the `masking_strict` variable in your `fides.toml` file is set to `false`.

## Example Auth0 Configuration

```yaml
saas_config:
  fides_key: <instance_fides_key>
  name: Auth0 SaaS Config
  type: auth0
  description: A sample schema representing the Auth0 connector for Fides
  version: 0.0.1

  connector_params:
    - name: domain
    - name: access_token

  client_config:
    protocol: https
    host: <domain>
    authentication:
      strategy: bearer
      configuration:
        token: <access_token>

  test_request:
    method: GET
    path: /api/v2/users-by-email
    query_params:
      - name: email
        value: test@test.com

  endpoints:
    - name: users
      requests:
        read:
          method: GET
          path: /api/v2/users-by-email
          query_params:
            - name: email
              value: <email>
          param_values:
            - name: email
              identity: email
        update:
          method: PATCH
          path: /api/v2/users/<user_id>
          body: |
            {
              <masked_object_fields>
            }
          param_values:
            - name: user_id
              references:
                - dataset: <instance_fides_key>
                  field: users.user_id
                  direction: from
    - name: user_logs
      requests:
        read:
          method: GET
          path: /api/v2/users/<user_id>/logs
          param_values:
            - name: user_id
              references:
                - dataset: <instance_fides_key>
                  field: users.user_id
                  direction: from
```

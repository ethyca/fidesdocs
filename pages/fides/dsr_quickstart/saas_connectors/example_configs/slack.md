
# Slack Enterprise

## Implementation Summary

Fides uses the following Slack Enterprise endpoints to retrieve and delete Personally Identifiable Information (PII) when processing a Privacy Request. Right to Access and Right to Delete (Right to Forget) support for each endpoint is noted below.

|Endpoint | Right to Access | Right to Delete |
|----|----|----|
|[Users](https://api.slack.com/methods/users.lookupByEmail) | Yes | Yes |


## Connection Settings

Connection instructions may be found in the [configuration guide](../saas_config).

**Deletion requests** are fulfilled by masking PII via `UPDATE` endpoints. To [give Fides permission](../../../installation/configuration#configuration-variable-reference) to remove PII using `DELETE` endpoints, ensure the `masking_strict` variable in your `fides.toml` file is set to `false`.

## Example Slack Enterprise Configuration

```yaml
saas_config:
  fides_key: <instance_fides_key>
  name: Slack Enterprise SaaS Config
  type: slack_enterprise
  description: A sample schema representing the Slack Enterprise connector for Fides
  version: 0.0.1

  connector_params:
    - name: domain
      default_value: slack.com
    - name: user_oauth_token
      label: User OAuth Token

  client_config:
    protocol: https
    host: <domain>
    authentication:
      strategy: bearer
      configuration:
        token: <user_oauth_token>

  test_request:
    method: GET
    path: /api/api.test

  endpoints:
    - name: user
      requests:
        read:
          method: GET
          path: /api/users.lookupByEmail
          query_params:
            - name: email
              value: <email>
          param_values:
            - name: email
              identity: email
          data_path: user
        delete:
          method: POST
          path: /api/admin.users.remove
          body: |
            {
               "team_id": <user_team_id>
               "user_id": <user_id>
            }
          param_values:
            - name: user_team_id
              references:
                - dataset: <instance_fides_key>
                  field: user.team_id
                  direction: from
            - name: user_id
              references:
                - dataset: <instance_fides_key>
                  field: user.id
                  direction: from
```

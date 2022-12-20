
# Firebase Auth

## Implementation Summary

Fides uses the following Firebase Auth endpoints to retrieve and delete Personally Identifiable Information (PII) when processing a Privacy Request. Right to Access and Right to Delete (Right to Forget) support for each endpoint is noted below.

|Endpoint | Right to Access | Right to Delete |
|----|----|----|
|[Users](https://firebase.google.com/docs/reference/rest/auth) | Yes | Yes |

## Connection Settings

Connection instructions may be found in the [configuration guide](../saas_config).

**Deletion requests** are fulfilled by masking PII via `UPDATE` endpoints. To [give Fides permission](../../../installation/configuration#configuration-variable-reference) to remove PII using `DELETE` endpoints, ensure the `masking_strict` variable in your `fides.toml` file is set to `false`.

## Example Firebase Auth Configuration

```yaml
saas_config:
  fides_key: <instance_fides_key>
  name: Firebase Auth SaaS Config
  type: firebase_auth
  description: A sample schema representing the Firebase Auth connector for Fides
  version: 0.0.1

  connector_params:
    - name: domain
      default_value: https://identitytoolkit.googleapis.com
    - name: type
      default_value: service_account
    - name: project_id
    - name: private_key_id
    - name: private_key
    - name: client_email
    - name: client_id
    - name: auth_uri
      default_value: https://accounts.google.com/o/oauth2/auth
    - name: token_uri
      default_value: https://oauth2.googleapis.com/token
    - name: auth_provider_x509_cert_url
      default_value: https://www.googleapis.com/oauth2/v1/certs
    - name: client_x509_cert_url

  client_config:
    protocol: NOT_USED
    host: NOT_USED

  test_request:
    method: POST
    path: /v1/accounts:createAuthUri

  endpoints:
    - name: user
      requests:
        read:
          request_override: firebase_auth_user_access
          param_values:
            - name: email
              identity: email
        update:
          request_override: firebase_auth_user_update
          param_values:
            - name: email
              identity: email
```

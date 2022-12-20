
# Braze

## Implementation Summary

Fides uses the following Braze endpoints to retrieve and delete Personally Identifiable Information (PII) when processing a Privacy Request. Right to Access and Right to Delete (Right to Forget) support for each endpoint is noted below.

|Endpoint | Right to Access | Right to Delete |
|----|----|----|
|[Export](https://www.braze.com/docs/api/endpoints/export/user_data/post_users_identifier#users-by-identifier-endpoint) | Yes | No |
|[Track](https://www.braze.com/docs/api/endpoints/user_data/post_user_track/) | Yes | No |
|[Status](https://www.braze.com/docs/api/endpoints/subscription_groups/get_list_user_subscription_groups#get-users-subscription-groups) | Yes | No |

## Connection Settings

Connection instructions may be found in the [configuration guide](../saas_config).

**Deletion requests** are fulfilled by masking PII via `UPDATE` endpoints. To [give Fides permission](../../../installation/configuration#configuration-variable-reference) to remove PII using `DELETE` endpoints, ensure the `masking_strict` variable in your `fides.toml` file is set to `false`.

## Example Braze Configuration

```yaml
saas_config:
  fides_key: <instance_fides_key>
  name: Braze SaaS Config
  type: braze
  description: A sample schema representing the Braze connector for Fides
  version: 0.0.2

  connector_params:
    - name: domain
    - name: api_key

  client_config:
    protocol: https
    host: <domain>
    authentication:
      strategy: bearer
      configuration:
        token: <api_key>

  test_request:
    method: GET
    path: /email/hard_bounces/
    query_params:
      - name: email
        value: test@test.com

  endpoints:
    - name: user
      requests:
        read:
          - method: POST
            path: /users/export/ids
            body: |
              {
                "email_address": "<email>",
                "fields_to_export": [
                    "apps",
                    "attributed_campaign",
                    "attributed_source",
                    "attributed_adgroup",
                    "attributed_ad",
                    "braze_id",
                    "campaigns_received",
                    "canvases_received",
                    "cards_clicked",
                    "country",
                    "created_at",
                    "custom_attributes",
                    "custom_events",
                    "devices",
                    "dob",
                    "email",
                    "email_subscribe",
                    "external_id",
                    "first_name",
                    "gender",
                    "home_city",
                    "language",
                    "last_coordinates",
                    "last_name",
                    "phone",
                    "purchases",
                    "push_subscribe",
                    "push_tokens",
                    "random_bucket",
                    "time_zone",
                    "total_revenue",
                    "uninstalled_at",
                    "user_aliases"
                ]
              }
            param_values:
              - name: email
                identity: email
            data_path: users
          - method: POST
            path: /users/export/ids
            body: |
              {
                "phone": "<phone_number>",
                "fields_to_export": [
                    "apps",
                    "attributed_campaign",
                    "attributed_source",
                    "attributed_adgroup",
                    "attributed_ad",
                    "braze_id",
                    "campaigns_received",
                    "canvases_received",
                    "cards_clicked",
                    "country",
                    "created_at",
                    "custom_attributes",
                    "custom_events",
                    "devices",
                    "dob",
                    "email",
                    "email_subscribe",
                    "external_id",
                    "first_name",
                    "gender",
                    "home_city",
                    "language",
                    "last_coordinates",
                    "last_name",
                    "phone",
                    "purchases",
                    "push_subscribe",
                    "push_tokens",
                    "random_bucket",
                    "time_zone",
                    "total_revenue",
                    "uninstalled_at",
                    "user_aliases"
                ]
              }
            param_values:
              - name: phone_number
                identity: phone_number
            data_path: users
        update:
          method: POST
          path: /users/track
          body: |
            {
              "attributes": [
                {
                  <all_object_fields>
                }
              ]
            }
    - name: subscription_groups
      requests:
        read:
          - method: GET
            path: /subscription/user/status
            query_params:
              - name: email
                value: <email>
            param_values:
              - name: email
                identity: email
            data_path: users
          - method: GET
            path: /subscription/user/status
            query_params:
              - name: phone
                value: <phone_number>
            param_values:
              - name: phone_number
                identity: phone_number
            data_path: users

```

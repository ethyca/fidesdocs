
# Square

## Implementation Summary

Fides uses the following Square endpoints to retrieve and delete Personally Identifiable Information (PII) when processing a Privacy Request. Right to Access and Right to Delete (Right to Forget) support for each endpoint is noted below.

|Endpoint | Right to Access | Right to Delete |
|----|----|----|
|[Customers](https://developer.squareup.com/reference/square/customers-api/search-customers) | Yes | Yes |
|[Locations](https://developer.squareup.com/reference/square/locations-api/list-locations) | Yes | Yes |
|[Orders](https://developer.squareup.com/reference/square/orders-api/search-orders) | Yes | Yes |

## Connection Settings

Connection instructions may be found in the [configuration guide](../saas_config).

**Deletion requests** are fulfilled by masking PII via `UPDATE` endpoints. To [give Fides permission](../../../installation/configuration#configuration-variable-reference) to remove PII using `DELETE` endpoints, ensure the `masking_strict` variable in your `fides.toml` file is set to `false`.

## Example Square Configuration

```yaml
saas_config:
  fides_key: <instance_fides_key>
  name: Square SaaS Config
  type: square
  description: A sample schema representing the Square connector for Fides
  version: 0.0.2

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
    path: /v2/customers

  endpoints:
    - name: customer
      requests:
        read:
          - method: POST
            path: /v2/customers/search
            param_values:
              - name: email
                identity: email
            body: |
              {
                "query": {
                  "filter": {
                    "email_address": {
                      "exact": "<email>"
                    }
                  }
                }
              }
            data_path: customers
          - method: POST
            path: /v2/customers/search
            param_values:
              - name: phone_number
                identity: phone_number
            body: |
              {
                "query": {
                  "filter": {
                    "phone_number": {
                      "exact": "<phone_number>"
                    }
                  }
                }
              }
            data_path: customers
        update:
          method: PUT
          path: /v2/customers/<customer_id>
          body: |
            {
              <masked_object_fields>
            }
          param_values:
            - name: customer_id
              references:
                - dataset: <instance_fides_key>
                  field: customer.id
                  direction: from
    - name: locations
      requests:
        read:
          method: GET
          path: /v2/locations
          param_values:
            - name: email
              identity: email
            - name: phone_number
              identity: phone_number
          data_path: locations
    - name: orders
      requests:
        read:
          method: POST
          path: /v2/orders/search
          param_values:
            - name: location_id
              references:
                - dataset: <instance_fides_key>
                  field: locations.id
                  direction: from
            - name: customer_id
              references:
                - dataset: <instance_fides_key>
                  field: customer.id
                  direction: from
          body: |
            {
                "location_ids": [
                    "<location_id>"
                ],
                "query": {
                    "filter": {
                        "customer_filter": {
                            "customer_ids": [
                                "<customer_id>"
                            ]
                        }
                    }
                }
            }
          data_path: orders
```

# Shopify

## Implementation Summary

Fides uses the following Shopify endpoints to retrieve and delete Personally Identifiable Information (PII) when processing a Privacy Request. Right to Access and Right to Delete (Right to Forget) support for each endpoint is noted below.

|Endpoint | Right to Access | Right to Delete |
|----|----|----|
|[Customers](https://shopify.dev/api/admin-rest/2022-01/resources/customer) | Yes | Yes |
|[Customer Orders](https://shopify.dev/api/admin-rest/2022-01/resources/customer#get-customers-customer-id-orders) | Yes | Yes |
|[Customer Addresses](https://shopify.dev/api/admin-rest/2022-01/resources/customer-address) | Yes | Yes |
|[Transactions](https://shopify.dev/api/admin-rest/2022-01/resources/transaction) | Yes | Yes |
|[Blogs](https://shopify.dev/api/admin-rest/2022-01/resources/blog) | Yes | No |
|[Articles](https://shopify.dev/api/admin-rest/2022-01/resources/article) | Yes | No |
|[Comments](https://shopify.dev/api/admin-rest/2022-01/resources/comment) | Yes | Yes |


## Connection Settings

Connection instructions may be found in the [configuration guide](../saas_config).

**Deletion requests** are fulfilled by masking PII via `UPDATE` endpoints. To [give Fides permission](../../../installation/configuration#configuration-variable-reference) to remove PII using `DELETE` endpoints, ensure the `masking_strict` variable in your `fides.toml` file is set to `false`.

## Example Shopify Configuration

```yaml
saas_config:
  fides_key: <instance_fides_key>
  name: Shopify SaaS Config
  type: shopify
  description: A sample schema representing the Shopify connector for Fides
  version: 0.0.1

  connector_params:
    - name: domain
    - name: access_token

  client_config:
    protocol: https
    host: <domain>
    authentication:
      strategy: api_key
      configuration:
        headers:
          - name: X-Shopify-Access-Token
            value: <access_token>

  test_request:
    method: GET
    path: /admin/api/2022-07/customers.json
    query_params:
      - name: email
        value: test+connector@test.com

  endpoints:
    - name: customers
      requests:
        read:
          method: GET
          path: /admin/api/2022-07/customers.json
          query_params:
            - name: email
              value: <email>
          param_values:
            - name: email
              identity: email
          data_path: customers
          pagination:
            strategy: link
            configuration:
              source: headers
              rel: next
        update:
          method: PUT
          path: /admin/api/2022-07/customers/<customer_id>.json
          body: |
            {
              "customer": { <masked_object_fields> }
            }
          param_values:
            - name: customer_id
              references:
                - dataset: <instance_fides_key>
                  field: customers.id
                  direction: from
    - name: customer_orders
      requests:
        read:
          method: GET
          path: /admin/api/2022-07/customers/<customer_id>/orders.json
          query_params:
            - name: status
              value: any
          param_values:
            - name: customer_id
              references:
                - dataset: <instance_fides_key>
                  field: customers.id
                  direction: from
          pagination:
            strategy: link
            configuration:
              source: headers
              rel: next
          data_path: orders
        update:
          method: PUT
          path: /admin/api/2022-07/orders/<order_id>.json
          body: |
            {
              "order" : { <masked_object_fields> }
            }
          param_values:
            - name: order_id
              references:
                - dataset: <instance_fides_key>
                  field: customer_orders.id
                  direction: from
    - name: customer_addresses
      requests:
        read:
          method: GET
          path: /admin/api/2022-07/customers/<customer_id>/addresses.json
          param_values:
            - name: customer_id
              references:
                - dataset: <instance_fides_key>
                  field: customers.id
                  direction: from
          pagination:
            strategy: link
            configuration:
              source: headers
              rel: next
          data_path: addresses
        update:
          method: PUT
          path: /admin/api/2022-07/customers/<customer_id>/addresses/<address_id>.json
          body: |
            {
              "customer_address": { <masked_object_fields> }
            }
          param_values:
            - name: customer_id
              references:
                - dataset: <instance_fides_key>
                  field: customer_addresses.customer_id
                  direction: from
            - name: address_id
              references:
                - dataset: <instance_fides_key>
                  field: customer_addresses.id
                  direction: from
    - name: customer_order_transactions
      requests:
        read:
          method: GET
          path: /admin/api/2022-07/orders/<order_id>/transactions.json
          param_values:
            - name: order_id
              references:
                - dataset: <instance_fides_key>
                  field: customer_orders.id
                  direction: from
          data_path: transactions
    - name: blogs
      requests:
        read:
          method: GET
          path: /admin/api/2022-07/blogs.json
          param_values:
            - name: email
              identity: email
          data_path: blogs
    - name: blog_articles
      requests:
        read:
          method: GET
          path: /admin/api/2022-07/blogs/<blog_id>/articles.json
          param_values:
            - name: blog_id
              references:
                - dataset: <instance_fides_key>
                  field: blogs.id
                  direction: from
          data_path: articles
    - name: blog_article_comments
      requests:
        read:
          method: GET
          path: /admin/api/2022-07/comments.json
          grouped_inputs: [blog_id, article_id]
          query_params:
            - name: blog_id
              value: <blog_id>
            - name: article_id
              value: <article_id>
          param_values:
            - name: blog_id
              references:
                - dataset: <instance_fides_key>
                  field: blog_articles.blog_id
                  direction: from
            - name: article_id
              references:
                - dataset: <instance_fides_key>
                  field: blog_articles.id
                  direction: from
          data_path: comments
          postprocessors:
            - strategy: filter
              configuration:
                field: email
                value:
                  identity: email
        update:
          method: PUT
          path: /admin/api/2022-07/comments/<comment_id>.json
          body: |
            {
              "comment": { <masked_object_fields> }
            }
          param_values:
            - name: comment_id
              references:
                - dataset: <instance_fides_key>
                  field: blog_article_comments.id
                  direction: from
```

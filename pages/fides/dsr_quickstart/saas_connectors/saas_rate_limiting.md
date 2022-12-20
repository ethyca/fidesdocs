# Rate Limiting 

_Rate limits_ can be used to restrict how fast requests can be made to SaaS endpoints. Rate limiting works across Fides instances that share a Redis cluster. 

A rate limit configuration can be set the within the [client configuration](./saas_config#client-config) for a SaaS connection. They can also be set within the endpoint request configuration, which will override the client configuration for that specific endpoint.

## Configuration details
| Attribute | Type | Description |
| --- | --- | --- |
| `enabled` | `bool` | *Optional.* Determines if the rate limiter is enabled. Default is `true`. |
| `limits` | `list`([RateLimit](#rate-limit-configuration)) | *Optional.* A list of RateLimit objects which can define multiple rate limits for endpoint requests.

Fides implements rate limiting as a fixed-window algorithm. Epoch seconds are divided into discrete buckets based on the configured period. 

| Attribute | Type | Description |
| --- | --- | --- |
|`rate` | `int` | Number of calls which are allow for the specified period. |
| `period` | `str` | The time period for which to limit calls. Allows values are `second`, `minute`, `hour`, `day`. |
| `custom_key` | `str` | *Optional.* An optional key which can be used to group rate usage across endpoints or connectors. |

## Examples

### Limit connections
In this example, the connector is limited to 10 TPS for all endpoints:

```yaml
saas_config:
  fides_key: my_connector

rate_limit_config:
    limits:
    - rate: 10
      period: second
```

### Disable limits
In this example, the connector request rate is limited, but the limiter is disable for `my_non_limited_entity` read requests:

```yaml
saas_config:
  fides_key: my_connector

  rate_limit_config:
    limits:
    - rate: 10
      period: second

  endpoints:
  - name: my_non_limited_entity
    requests:
      read:
        rate_limit_config:
          enabled: false

```

### Share rate usage
In this example, `my_connector_1` and `my_connector_2` share the custom key `shared_custom_key`, and so both contribute requests to the same time window:

```yaml
saas_config:
  fides_key: my_connector_1

rate_limit_config:
    limits:
    - rate: 10
      period: second
      custom_key: shared_custom_key

  fides_key: my_connector_2

rate_limit_config:
    limits:
    - rate: 10
      period: second
      custom_key: shared_custom_key
```

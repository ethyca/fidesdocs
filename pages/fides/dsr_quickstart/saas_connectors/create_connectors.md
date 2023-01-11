import Callout from 'nextra-theme-docs/callout'

# Create New Connections

A [connection](../configure_connectors) links Fides to you your databases or third-party applications, allowing Fides to retrieve and update selected PII categories when fulfilling privacy requests. Connections are made up of _configurations_, which define how to access your third-party services; and _datasets_, which represent the data to retrieve or mask.

## Create a new connection

### Create a dataset 

A Fides [dataset](../dsr_support/datasets) consists of a declaration of fields, with metadata describing how those fields are related. Fides uses these declarations to know which fields to update or retrieve in your third-party service, and navigates between different collections by referencing their relationships.

The following is an example Dataset declaration, representing a simple `conversations` table:

```yaml
dataset:
  - fides_key: mock_dataset
    name: Mock Dataset
    description: A mock dataset.
    collections:
     - name: conversations
        fields:
          - name: id
            data_categories: [user.unique_id]
            fides_meta:
               primary_key: True
          - name: campaign_id
            data_categories: [system.operations]
          - name: list_id
            data_categories: [system.operations]
          - name: from_email
            data_categories: [user.contact.email]
          - name: from_label
            data_categories: [user.contact.email]
          - name: subject
            data_categories: [user]
```

The above dataset defines a conversations table with a `primary_key` of `id`, and several other attributes which represent matching fields in the datastore:

| `dataset` | Description |
| -- | ----- |
| `fides_key` | A unique identifier for your dataset.
| `name` | A human-readable name for your dataset.
| `description` | _Optional_. A human-readable description of your dataset.
| `collections` | A list of addressable collections which contain the data you would like Fides to retrieve or update.

| `collections` | Description |
| -- | ----- |
| `name` | The name of the collection in your dataset must correspond to the name used for it in your datastore. Fides will use this name to generate query and update statements.
| `fields` | A list of addressable fields in the collection. 

| `fields` | Description |
| -- | ----- |
| `name` | The name of the field will be used to generate query and update statements. Fides will only attempt to access and update information from the fields you declare.
| `data_categories` |Annotating data_categories connects fields to execution policies, allowing Fides to know what actions to take based on the contained data. For more information, see the guide on [execution policies](../dsr_support/execution_policies). 
| `fides_meta` | The `fides_meta` section specifies additional fields that control how Fides manages your data.


| `fides_meta` | Description |
| -- | ----- |
| `data_type` | _Optional_. The type of data held by this field. Data types are used to convert values when generating queries. This is especially necessary when using data of one type to help locate data of another type. Available data types are string, integer, float, boolean, and object_id. object types are also supported for MongoDB.
| `primay_key` | _Optional_. Either `true` or `false`. If true, Fides will treat this field as a unique row identifier for generating update statements.


<Callout> Additional dataset attributes can be found in the dataset [resources guide](../dsr_support/datasets). </Callout>

## Define your dataset
Using the above example, you can now create a dataset representing the datastore you will connect to. Fides provides several dataset templates for common applications in the public repository.

Start by adding in the datastore information, as well as any fields you would like to access:
```yaml
dataset:
  - fides_key: mock_dataset
    name: Mock Dataset
    description: A dataset to a third-party service.
    collections:
      - name: <field name> #your collection name
        fields:
          - name: <field name> #your first field
          - name: <field name> #your second field
          - name: <field name> #your third field
... 
```

### Add `data_category` annotations
For each of your fields, provide any relevant data_category information.
```yaml
... 
       fields:
          - name: id
            data_categories: [system.operations]
...
```

The above [user.unique_id] data category defines the `id` field as a unique identifier for a user assigned through system use. Additional default data categories can be found in the fideslang taxonomy.

### Add `fides_meta` information
For each of your fields, provide any relevant `fides_meta` information.
```yaml
... 
       fields:
          - name: id
            data_categories: [user.unique_id]
            fides_meta:
              primary_key: True
...
```

The above `primary_key` defines the `id` field as the primary key to use in any queries in the address collection. Additional uses of the `fides_meta` tag can be found in the [dataset guide](../dsr_support/datasets).

## Create a SaaS configuration
A SaaS [configuration schema](./saas_config) represents a collection of HTTP requests. The configuration defines how to interact with your SaaS application, including how to update and delete the data defined in your dataset.

The following is an example SaaS config declaration, which consists of a single HTTP request, and references the fields defined in the above dataset:
```yaml
saas_config:
  fides_key: connector_example
  name: Example SaaS Config
  type: custom
  description: A sample schema representing a single HTTP request.
  version: 1.0.0

  connector_params:
    - name: domain
    - name: username
    - name: api_key

  client_config:
    protocol: https
    host: <domain>
    authentication:
      strategy: basic
      configuration:
        username: <username>
        password: <api_key>

  endpoints:
  - name: messages
    requests:
      read:
        method: GET
        path: /3.0/conversations/<conversation_id>/messages
        param_values:
          - name: conversation_id
            references:
              - dataset: mock_dataset
                field: conversations.id
                direction: from
        data_path: conversation_messages        
```

The above configuration defines the following:
    *  The domain and authentication requirements for an HTTP client - in this case, [Mailchimp](./example_configs/mailchimp)
    * A `GET` request for the Mailchimp messages resource


| `saas_config` | Description |
| -- | ----- |
| `fides_key` | Used to uniquely identify the SaaS config.
|`name` | A human-readable name for your configuration.
| `type` | The type of connector, matching either an available configuration that best reflects the API/SaaS provider.. 
| `description` | _Optional_. A human-readable description of your configuration.
| `version` | Used to track different versions of the configuration.
| `connector_params` | A list of settings that must be configured as part of connecting to the third-party service. In the case of secret information (e.g., `password`, `api_key`), these are variable names Fides will use to insert your securely stored secret values. |
| `client_config` | The necessary information to create a base HTTP client. Note that host, username, and password are contained in angle brackets and not defined. These reference corresponding connector_params, and allow Fides to replace them with stored values. |
| `endpoints` | A list of HTTP request types and their names.


| `endpoints` | Description |
| -- | ----- |
| `name` |The endpoint name corresponds to a collection in the corresponding dataset.
| `request` | A list of settings that define how Fides will access and update each collection in the corresponding dataset.


| `request` | Description |
| -- | ----- |
| `method` | The HTTP method used for the endpoint.
| `path` | A static or dynamic resource path. Dynamic portions of the path are enclosed within angle brackets (`<dynamic_value>`), and are replaced with corresponding param_values.
| `data_path` | The expression used to access the collection information from the raw JSON response.
| `param_values` | A series of values referenced from your dataset. Fides will use these values to replace any dynamic values used in the request. 

| `param_values` | Description |
| -- | ----- |
| `name` | Used as the key to reference this value in the path or other fields.
| `references` | A list of attributes defining the source of the value in your dataset.
| `data_path` | The expression used to access the collection information from the raw JSON response.

Additional SaaS config attributes can be found in the [configuration documentation](./saas_config).

### Define your SaaS config
Using the above example, you can now create a SaaS configuration schema defining your connection to a third-party service . Fides provides several pre-built configurations for common applications in the public repository.

Start by adding in the meta-level configuration information:
```yaml
saas_config:
  fides_key: connector_example
  name: Example SaaS Config
  type: custom
  description: A sample schema representing a single HTTP request.
  version: 1.0.0

  connector_params:

  client_config:

  endpoints:
```

The above example defines the highest-level SaaS config fields, and inserts placeholders for the remaining sections.

### Add your connection information
For your `connector_params`, provide any relevant information to connect to your SaaS application.
```yaml
... 
  connector_params:
    - name: domain
    - name: username
    - name: api_key
...
```

The above `connector_params` outline a `domain`, `username`, and `api_key` used for connecting to Mailchimp. These reference values Fides will dynamically insert from your stored secrets, which will be created in the next step. Additional information can be found in the connector params documentation.

For your `client_config`, provide any relevant information to create an HTTP client.
```yaml
...
  client_config:
    protocol: https
    host: <domain>
    authentication:
      strategy: basic
      configuration:
        username: <username>
        password: <api_key>
...
```

The above `client_config` outlines an `https` protocol, and uses a domain dynamic value reference from your `connector_params`. It also defines a basic authentication strategy, which references the `username` and `api_key` dynamic values. 

Additional options for authentication are found in the client configuration [documentation](./saas_config#client-config).

### Add your endpoints
You can now define your endpoints:
```yaml
  endpoints:
  - name: messages
    requests:
      read:
        method: GET
        path: /3.0/conversations/<conversation_id>/messages
        param_values:
          - name: conversation_id
            references:
              - dataset: mock_dataset
                field: conversations.id
                direction: from
        data_path: conversation_messages       
```

The above definition specifies a `GET` request with `read` access. The path’s `<conversation_id>` will be replaced with the `conversation_id` from the `param_values`, which is referenced from the `conversations.id` field defined in your `mock_dataset`. 

This request will read and return the result of the messages endpoint for the given `conversation_id`.

## Configure the SaaS connector
You can now finalize your SaaS connector, which will store your connection secrets, and associate your dataset and SaaS configuration.

The following configuration steps are best followed using the built-in API documentation (`http://{server_url}/docs` when your web server is running), or via curl.

### Create your connection
```json
PATCH api/v1/connection
[
  {
    "name": "Mock Application",
    "key": {saas_key},
    "connection_type": "saas",
    "access": "read"
  }
]
```

| Attribute | Description |
| --- | ---- |
| `name` | A human-readable name for your connection.
| `key` | A unique ID for your connection.
| `connection_type` | `saas`
| `access` | The connection’s permissions, either read or write.


### Add the SaaS config
```json
PATCH api/v1/connection/{saas_key}/saas_config
{
  "fides_key": "connector_example",
  "name": "Example SaaS Config",
  "type": "custom",
  "description": "A sample schema representing a single HTTP request." ...
}
```

The `saas_config` endpoint accepts the entirety of your SaaS config in JSON format.

### Configure the secrets
```json
PUT api/v1/connection/{saas_key}/secret
{
  "domain": "{domain}",
  "username": "{username}",
  "api_key": "{api_key}"
}
```

The secrets configured in this request will match the `connector_params` referenced in your SaaS config. Your SaaS configuration must already be defined to provide validation for the secrets.

### Add the dataset
```json
PUT api/v1/connection/{saas_key}/dataset
[
  {
    "fides_key": "mock_dataset",
    "name": "Mock Dataset",
    "description": "A mock dataset.",
    "collections":[
     {
        "name": "conversations"
    ...
  }
]
```
The `dataset` endpoint accepts the entirety of your dataset in JSON format.

### Test the connector
You can now test your connection with the following endpoint, which will verify your secrets:
```
/api/v1/connection/{saas_key}/test
```

With your connector now configured, you are ready to utilize it in processing subject requests with Fides.

## Configure a webhook
A [webhook](../dsr_support/policy_webhooks) is an HTTPS callback that calls an external REST API endpoint before or after a privacy request executes. Multiple webhooks can exist on a single policy, and are defined as either one-way (where Fides does not wait for a response), or two-way (where Fides will wait for a response before proceeding). 

Configuring a webhook is similar to configuring a SaaS connection. The following configuration steps are best followed using the interactive API documentation (`http://{server_url}/docs` when your web server is running) or via curl.

### Create your connection
```json
PATCH api/v1/connection
[
  {
    "name": "Mock Webhook",
    "key": "mock_webhook_key",
    "connection_type": "https",
    "access": "read"
  }
]
```

Unlike the SaaS connector, the `connection_type` of your webhook is set to `https`.

### Configure the secrets
```json
PUT /v1/connection/{webhook_key}/secret
{
  "url": "string",
  "username": "string",
  "password": "string",
  "host": "string",
  "port": 0,
  "defaultauthdb": "string"
}
```

The secret details needed to talk to your API endpoint are defined by making a PUT to the connection’s `secrets` endpoint. These credentials are stored and encrypted in the Fides app database.

### Configure pre- or post-webhook settings
Now that your webhook is connected, you can configure whether it runs before or after a privacy request, as well as if it is a one- or two-way webhook. Note that these endpoints apply the pre- or post- execution rule to your Fides execution policy.
```json
PUT /policy/{policy_key}/webhook/pre_execution
[
    {
        "connection_config_key": "mock_webhook_key",
        "direction": "one_way",
        "key": "wake_up_snowflake_db",
        "name": "Wake up Snowflake DB Webhook"
    },
     {
        "connection_config_key": "mock_webhook_key",
        "direction": "two_way",
        "key": "prep_systems_webhook",
        "name": "Prep Systems Webhook"
    }
]
```

This creates two webhooks that are run sequentially before a privacy request runs. The first might wake up a database and move on; the second could prep systems, and wait for a successful response before continuing.

In both cases, the `connection_config_key` references the `key` made when the webhook was created.

The following endpoint is available to create post webhooks, which run after a privacy request:
```
PUT /policy/{policy_key}/webhook/post_execution
```

### Test the webhook
You can now test your webhook connection with the following endpoint, which will verify your secrets:
```
/api/v1/connection/{webhook_key}/test
```

Congratulations! You've built and tested your own SaaS configuration file. You can now reference it when processing [DSRs](../dsr_processing).





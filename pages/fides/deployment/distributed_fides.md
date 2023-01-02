# Deploying Fides in a Parent-Child Model

Fides’ distributed deployment model allows for the implementation of Fides nodes at both the organization and departmental levels. Each node is a fully configured instance of Fides. 

In this parent-child configuration, the parent Fides node supports a single Privacy Center, which is front-facing for the organization and receives all privacy requests. The child nodes service privacy requests sent from the parent node. 


## Implementation
### Requirements
This guide is intended as supplementary material to a standard [Fides implementation](../installation/advanced). 

To use Fides in a parent-child configuration, the following is required:
1. At least two standalone Fides environments, each with their own Fides app container, Admin UI, database, and Redis cache. 
    * One of these Fides environments is to be considered the parent Fides instance
    * All other Fides environments are considered child Fides instances
    <br />

2. If desired, the parent Fides should host a [Privacy Center](../dsr_quickstart/privacy_center). Child Fides instances will not host their own Privacy Centers.

3. There must be network connectivity from the parent Fides to each child Fides. Specifically, the parent Fides should be able to reach the port that the child Fides application web server is listening on. The parent Fides needs to reach the following routes on the child Fides:
    * `/api/v1/login`
    * `/api/v1/privacy-request`
    * `/api/v1/privacy-request?request_id=*`
    * `/api/v1/privacy-request/authenticated`
    * `/api/v1/privacy-request/transfer/*`

    <br />

4. In each Fides environment, a user must have appropriate scopes to act as a server administrator. There is no user synchronization between Fides environments. An [administrative user](../installation/configuration#security) should be configured for each child Fides.

5. The parent server will need to authenticate with each child Fides as a user that has these scopes
    * `privacy-request:create`
    * `privacy-request:read`
    * `privacy-request:transfer`

    “Parent user” credentials can be defined on each child Fides via environment/configuration variables (see the [Child configuration](#child-configuration), below). This user will be automatically created on the child Fides instance, and will have the necessary scopes listed above.


## Step-by-step configuration

### Child configuration
For each child Fides, complete the following configuration steps:

1. Set [configuration variables](../installation//configuration) to establish credentials that the parent will use for authentication:
```
FIDES__SECURITY__PARENT_SERVER_USERNAME: parent_username
FIDES__SECURITY__PARENT_SERVER_PASSWORD: some_secret_parent_password
```

2. Configure any [connections](../dsr_quickstart/configure_connectors), [policies](../dsr_quickstart//dsr_support/execution_policies), [rules](../dsr_quickstart/dsr_support/execution_policies#add-a-rule), and [storage locations](../dsr_quickstart//dsr_support/storage) that you want to execute on the given child Fides. 
    * All policies and rules that you wish to execute on a given child must also exist on the parent
    * Storage configurations, connector configurations, and datasets can (and likely should) differ between parent and child Fides instances
    <br />

3. To disable manual request approval (so that approval isn’t needed on a child Fides instance), you may optionally configure the following:
```
FIDES__EXECUTION__REQUIRE_MANUAL_REQUEST_APPROVAL: false
```

### Parent configuration
The following must be configured on the parent Fides **for each** child Fides that you’d like to connect:

1. A [connector configuration](../dsr_quickstart/dsr_support/database_connectors):

```json
PATCH /api/v1/connection 

[
  {
    "name": "my fides connector",
    "key": "fides_connector_connection_1",
    "connection_type": "fides",
    "access": "write"
  }
]
```
| Attribute | Description |
| --- | ---- |
| `name` | A unique user-friendly name for the child configuration.
| `key` | A unique key used to manage the child configuration. This is auto-generated from name if left blank. Accepted values are alphanumeric, _, and ..
| `connection_type` | Set to fides for a parent-child configuration.
| `access` | Set to write for a parent-child configuration.

3. [Secrets](../dsr_quickstart/dsr_support/database_connectors#set-the-connection-secrets) associated with the connector configuration, which define the connection details to the child Fides:

```json
PUT /api/v1/connection/{connection_key}/secret

{
  "uri": "http://fides:8080",
  "username": "parent_username",
  "password": "some_secret_parent_password"
}
```

| Attribute | Description |
| --- | ---- |
| `{connection_key}` | The key set in your initial connector configuration.
| `uri` | The location of the child.
| `username` | The `FIDES__SECURITY__PARENT_SERVER_USERNAME` set on the child.
| `password` | The `FIDES__SECURITY__PARENT_SERVER_PASSWORD` set on the child.
| `polling_timeout` | _Optional_. How long, in seconds, the parent Fides will wait for this child Fides to complete the execution of a given privacy request. Defaults to 1800 seconds (30 minutes). 
| `polling_interval` | _Optional_. How long, in seconds, the parent Fides will wait between its “checks” on the child to determine whether it has completed execution of a given privacy request. Defaults to 30 seconds. 

A [dataset](../dsr_quickstart/dsr_support/datasets) configuration. This needs needs only one collection, with one identity field:

```json
PATCH api/v1/connection/{connection_key}/dataset

[
    {
        "fides_key": "fides_example_child_test_dataset",
        "name": "Fides Child Example Test Dataset",
        "description": "Example of a Remote Fides Child Dataset",
        "collections": [
            {
                "name": "placeholder",
                "fields": [
                    {
                        "name": "placeholder",
                        "fidesops_meta": {
                            "identity": "email"
                        }
                    }
                ]
            }
        ]
    }
]
```


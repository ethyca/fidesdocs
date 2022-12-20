
# Twilio Conversations

## Implementation Summary

Fides uses the following Twilio Conversations endpoints to retrieve and delete Personally Identifiable Information (PII) when processing a Privacy Request. Right to Access and Right to Delete (Right to Forget) support for each endpoint is noted below.

|Endpoint | Right to Access | Right to Delete |
|----|----|----|
|[Users](https://www.twilio.com/docs/conversations/api/user-resource#fetch-a-specific-user-resource) | Yes | Yes |
|[User Conversations](https://www.twilio.com/docs/conversations/api/user-conversation-resource#list-all-of-a-users-conversations) | Yes | No |
|[Conversation Messages](https://www.twilio.com/docs/conversations/api/conversation-message-resource#list-all-conversation-messages) | Yes | Yes |
|[Conversation Participants](https://www.twilio.com/docs/conversations/api/conversation-participant-resource#read-multiple-conversationparticipant-resources) | Yes | No |


## Connection Settings

Connection instructions may be found in the [configuration guide](../saas_config).

**Deletion requests** are fulfilled by masking PII via `UPDATE` endpoints. To [give Fides permission](../../../installation/configuration#configuration-variable-reference) to remove PII using `DELETE` endpoints, ensure the `masking_strict` variable in your `fides.toml` file is set to `false`.

## Example Twilio Conversations Configuration

```yaml
saas_config:
  fides_key: <instance_fides_key>
  name: Twilio Conversations SaaS Config
  type: twilio_conversations
  description: A sample schema representing the Twilio Conversations connector for Fidesops
  version: 0.0.2

  connector_params:
    - name: domain
      default_value: conversations.twilio.com
    - name: account_id
    - name: password

  external_references:
    - name: twilio_user_id
      label: Twilio User ID
      description: Dataset reference to the location of Twilio user SIDs

  client_config:
    protocol: https
    host: <domain>
    authentication:
      strategy: basic
      configuration:
        username: <account_id>
        password: <password>

  test_request:
    method: GET
    path: /v1/Users
    query_params:
      - name: PageSize
        value: 1

  endpoints:
    - name: user
      requests:
        read:
          method: GET
          path: /v1/Users/<sid>
          param_values:
            - name: sid
              references:
                - twilio_user_id
        update:
          request_override: twilio_user_update
          param_values:
            - name: sid
              references:
                - dataset: <instance_fides_key>
                  field: user.sid
                  direction: from
    - name: user_conversations
      requests:
        read:
          method: GET
          path: /v1/Users/<user_id>/Conversations
          query_params:
            - name: PageSize
              value: 1000
          param_values:
            - name: user_id
              references:
                - dataset: <instance_fides_key>
                  field: user.sid
                  direction: from
          pagination:
            strategy: link
            configuration:
              source: body
              path: meta.next_page_url
          data_path: conversations
    - name: conversation_messages
      requests:
        read:
          method: GET
          path: /v1/Conversations/<conversation_id>/Messages
          query_params:
            - name: PageSize
              value: 1000
          param_values:
            - name: conversation_id
              references:
                - dataset: <instance_fides_key>
                  field: user_conversations.conversation_sid
                  direction: from
          pagination:
            strategy: link
            configuration:
              source: body
              path: meta.next_page_url
          data_path: messages
        update:
          request_override: twilio_conversation_message_update
          param_values:
            - name: conversation_id
              references:
                - dataset: <instance_fides_key>
                  field: conversation_messages.conversation_sid
                  direction: from
            - name: message_id
              references:
                - dataset: <instance_fides_key>
                  field: conversation_messages.sid
                  direction: from
    - name: conversation_participants
      requests:
        read:
          method: GET
          path: /v1/Conversations/<conversation_id>/Participants
          query_params:
            - name: PageSize
              value: 1000
          param_values:
            - name: conversation_id
              references:
                - dataset: <instance_fides_key>
                  field: user_conversations.conversation_sid
                  direction: from
          pagination:
            strategy: link
            configuration:
              source: body
              path: meta.next_page_url
          data_path: participants
```

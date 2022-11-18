# Configure Connectors

Fides *Connections* represent integrations to third party applications, databases, or manual storage locations.

To get started, navigate to your hosted UI, which is available at `http://{server_url}/` (e.g. `http://localhost:8080/`) when your webserver is running. 

## Add a connection

To add a new Connector, select "**Create New Connector**" from the Connections panel. You will be directed to a list of all available connection options, including adding [manual connectors](#manual-connections).

Search for and select your desired connector. The Connectors UI will assist in adding and configuring your new connection. 

### Automated connections

Fides automatically includes your SaaS connectors when processing [privacy requests](../getting-started/privacy_requests.md). Once you have selected a connection type, the UI allows you to describe your connection's configuration information, which includes any necessary fields for accessing and updating third-party data.

Once you have filled in the necessary information, select **Save**, and Fides will automatically attempt to test your connection.

### Manual connections

Manual connections are available for any data that cannot be processed automatically. Examples of manual connections might include physical storage locations, or third-party services without accessible APIs. Fides will pause processing a privacy request at any manual connection, and wait for administrator input before continuing.

To add a manual connection, select **Manual connectors** in the "Show all connectors" dropdown. 

Once selected, Fides allows you to name and describe your manual connection, as well as provide a list of connection owners. These owners will be contacted by email when needed to respond to a privacy request with a manual component.

To continue, select **Save**.

You may now add any fields required by the manual connection. Connection owners will be required to supply this information when processing a manual request.

Additional fields may be added by selecting **Add new PII Fields**. 

One finished, select **Save.**

## Next steps
With your connections configured, you are read to connect Fides to your [databases](./connect_databases.md).
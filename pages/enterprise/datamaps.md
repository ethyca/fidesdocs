# Data Map Automation

A _data map_ represents a centralized view your organization, its systems, and the data any given system collects or stores. This map identifies the categories of sensitive or personal information collected by your systems, documents reasons for data processing and use, and tracks data formats, locations, and retention policies. 

Connecting your systems to Fides empowers your privacy stakeholders to understand the flow of data throughout your organization, and generate customized reports at the click of a button.

![Datamap](../../public/assets/img/enterprise/datamap_overview.png)

## Generate your datamap

In Fides, _systems_ describe any services that store or process data for your organization, including third-party APIs, web applications, databases, and data warehouses. Fides ships with automated infrastructure discovery capabilities, making detecting and adding new systems to your data map a straightforward process.

![Datamap](../../public/assets/img/enterprise/datamap_generate.png)

Use any of the provided options to add your systems: [infrastructure scanners](../fides/data_mapping/scanners) for Okta and AWS, [data flow scanners](./dataflow_scanner) for your runtime environment, and [manual additions](../fides/data_mapping/scanners#manual-system-generation) for all other services.

## Manage your data map

With your systems added, your data map is ready to use! 

![Datamap](../../public/assets/img/enterprise/datamap_overview.png)

The Fides data map provides several additional features to assist in viewing your organization and systems. 

Each view can be filtered by system resource metadata, to highlight and focus on systems with particular characteristics:
![Datamap](../../public/assets/img/enterprise/datamap_filter.png)

You may inspect your systems through both the visual map and a customizable tabular view, or export your results in multiple formats, including a GDPR-compliant Article 30 Record of Processing Activities: 

![Datamap](../../public/assets/img/enterprise/datamap_report.png)

From the tabular view, the report can be customized to contain only the columns and data of interest:

![Datamap](../../public/assets/img/enterprise/datamap_settings.png)


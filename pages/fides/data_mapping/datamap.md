import Callout from 'nextra-theme-docs/callout'

# Export a Data Map

<Callout>The open-source edition of Fides allows you to export a data map of your resources using the CLI. A data map visualization is available in [Fides Enterprise](../../enterprise/datamaps).</Callout> 

## Export your resources

To export the resources generated in the previous step, navigate to the root Fides directory in your terminal and run the following command:

```sh title="Push and Export Defaults"
fides export datamap --output-dir data_map/
```

This will export a data map to a `data_map` directory. To make changes, you may use the UI to add, remove, or update scanned systems, add [datasets](../dsr_quickstart/connect_databases), or manually document additional resources. 

## Next steps
Congratulations - you've created your first data map! Fides offers many more tools help take control of your data privacy. To find out more, check out our guides on [DSR automation](../dsr_quickstart/overview), [privacy-as-code](../cli_support/cicd), and the [Fides Taxonomy](https://ethyca.github.io/fideslang/).
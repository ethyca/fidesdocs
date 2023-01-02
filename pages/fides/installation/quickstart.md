import Callout from 'nextra-theme-docs/callout'

# Get Started with Fides

<Callout emoji="ⓘ">This guide is designed to get you started with Fides in as few steps as possible. Want to learn about configuration in-depth? Check out our [advanced setup](./advanced) and [deployment](../deployment/deployment) guides!</Callout>

## Minimum requirements
See the the [project requirements](../installation/requirements) to get started.

**If using Docker,** run `docker -v` in a new shell to confirm that docker is ready to use:

```bash title="Example output:"
% docker -v
Docker version 20.10.11, build dea9396
```

**If using Python,** run `python --version` in a new shell to confirm that docker is ready to use:

```bash title="Example output:"
% python --version
Python 3.9.13
```

Once you are sure Docker or Python is running, you are ready to install Fides!

### Using Docker 
Run the following command to pull the latest image from Ethyca's [DockerHub](https://hub.docker.com/r/ethyca/fides):

```
docker pull ethyca/fides
```

To pull the Fides [Privacy Center](../consent/privacy_center), run the following:
```
docker pull ethyca/fides-privacy-center
```

### Running the webserver
Once downloaded, you can start the Fides UI and database:
```
docker run ethyca/fides
```

With the Fides webserver running, the hosted UI is available at `http://{server_url}/` (e.g. `http://localhost:8080/`). 

---

## Using Pip
The Fides Python package is [published on PyPI](https://pypi.org/project/fides/) and maintained by the Fides team.
### Installation 
To install Fides, run:

```sh
pipx install fides
```
#### Initialize Fides

Initializing the project will create a configuration file with default values, and generate a directory to house your Fides resources.

```sh title="Initialize Fides"
fides init
```

```txt title="Expected Output"
Initializing Fides...
----------
Created a './.fides' directory.
----------
Created a fides config file: ./.fides/fides.toml
To learn more about configuring fides, see:
    https://ethyca.github.io/fides/installation/configuration/
----------
For example policies and help getting started, see:
    https://ethyca.github.io/fides/guides/policies/
----------
Fides initialization complete.
```

#### Running the webserver

In a shell, run the following command:

```sh
fides webserver
```

With the Fides webserver running, the hosted UI is available at `http://{server_url}/` (e.g. `http://localhost:8080/`). 

## Next steps
For more information on customizing your environment configuration, see the [configuration reference](../installation/configuration) guide.

Now that Fides is up and running, you're ready to get started with [DSR automation](../dsr_quickstart/overview) and [data mapping](../data_mapping/overview)!
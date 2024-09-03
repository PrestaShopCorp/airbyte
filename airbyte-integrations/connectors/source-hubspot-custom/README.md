# Hubspot Custom source connector

This is the repository for the Hubspot source connector, written in Python.
For information about how to use this connector within Airbyte, see [the documentation](https://docs.airbyte.com/integrations/sources/hubspot).

### Changes

Changes from the original Hubspot Connector are :

- **Deals** :
  - Filter records on Pipeline = Sales pipeline Addons (062f0eb3-9976-4074-a67c-9d5e177432a1)

## Local development

### Prerequisites

- Python (~=3.9)
- Poetry (~=1.7) - installation instructions [here](https://python-poetry.org/docs/#installation)

### Installing the connector

From this connector directory, run:

```bash
poetry install --with dev
```

### Create credentials

**If you are a community contributor**, follow the instructions in the [documentation](https://docs.airbyte.com/integrations/sources/hubspot)
to generate the necessary credentials. Then create a file `secrets/config.json` conforming to the `source_hubspot_custom/spec.yaml` file.
Note that any directory named `secrets` is gitignored across the entire Airbyte repo, so there is no danger of accidentally checking in sensitive information.
See `sample_files/sample_config.json` for a sample config file.

### Locally running the connector

```
poetry run source-hubspot-custom spec
poetry run source-hubspot-custom check --config secrets/config.json
poetry run source-hubspot-custom discover --config secrets/config.json
poetry run source-hubspot-custom read --config secrets/config.json --catalog integration_tests/basic_read_catalog.json
```

### Running unit tests

To run unit tests locally, from the connector directory run:

```
poetry run pytest unit_tests
```

### Building the docker image

1. Install [`airbyte-ci`](https://github.com/airbytehq/airbyte/blob/master/airbyte-ci/connectors/pipelines/README.md)
2. Run the following command to build the docker image:

```bash
airbyte-ci connectors --name=source-hubspot-custom build
```

An image will be available on your host with the tag `airbyte/source-hubspot-custom:dev`.

### Running as a docker container

Then run any of the connector commands as follows:

```
docker run --rm airbyte/source-hubspot-custom:dev spec
docker run --rm -v $(pwd)/secrets:/secrets airbyte/source-hubspot-custom:dev check --config /secrets/config.json
docker run --rm -v $(pwd)/secrets:/secrets airbyte/source-hubspot-custom:dev discover --config /secrets/config.json
docker run --rm -v $(pwd)/secrets:/secrets -v $(pwd)/integration_tests:/integration_tests airbyte/source-hubspot-custom:dev read --config /secrets/config.json --catalog /integration_tests/configured_catalog.json
```

### Running our CI test suite

You can run our full test suite locally using [`airbyte-ci`](https://github.com/airbytehq/airbyte/blob/master/airbyte-ci/connectors/pipelines/README.md):

```bash
airbyte-ci connectors --name=source-hubspot-custom test
```

### Customizing acceptance Tests

Customize `acceptance-test-config.yml` file to configure acceptance tests. See [Connector Acceptance Tests](https://docs.airbyte.com/connector-development/testing-connectors/connector-acceptance-tests-reference) for more information.
If your connector requires to create or destroy resources for use during acceptance tests create fixtures for it and place them inside integration_tests/acceptance.py.

### Dependency Management

All of your dependencies should be managed via Poetry.
To add a new dependency, run:

```bash
poetry add <package-name>
```

Please commit the changes to `pyproject.toml` and `poetry.lock` files.

## Publishing a new version of the connector

You've checked out the repo, implemented a million dollar feature, and you're ready to share your changes with the world. Now what?

1. Make sure your changes are passing our test suite: `airbyte-ci connectors --name=source-hubspot-custom test`
2. Bump the connector version (please follow [semantic versioning for connectors](https://docs.airbyte.com/contributing-to-airbyte/resources/pull-requests-handbook/#semantic-versioning-for-connectors)):
   - bump the `dockerImageTag` value in in `metadata.yaml`
   - bump the `version` value in `pyproject.toml`
3. Make sure the `metadata.yaml` content is up to date.
4. Make sure the connector documentation and its changelog is up to date (`docs/integrations/sources/hubspot.md`).
5. Create a Pull Request: use [our PR naming conventions](https://docs.airbyte.com/contributing-to-airbyte/resources/pull-requests-handbook/#pull-request-title-convention).
6. Pat yourself on the back for being an awesome contributor.
7. Someone from Airbyte will take a look at your PR and iterate with you to merge it into master.
8. Once your PR is merged, the new version of the connector will be automatically published to Docker Hub and our connector registry.

Build the docker image then run the following : 

```sh
docker tag airbyte/source-hubspot-custom:dev <ZONE>-docker.pkg.dev/<GCP-PROJECT-ID>/source-hubspot-custom/4.2.18:latest
docker push <ZONE>-docker.pkg.dev/<GCP-PROJECT-ID>/source-hubspot-custom/4.2.18:latest
```

*Example :*
```sh
docker tag airbyte/source-hubspot-custom:dev europe-west1-docker.pkg.dev/ps-data-prestashop-airbyte/source-hubspot-custom/v0:latest
docker push europe-west1-docker.pkg.dev/ps-data-prestashop-airbyte/source-hubspot-custom/v0:latest
```

airbyte-ci connectors --name=source-hubspot-custom build --architecture=linux/arm64

docker build --platform=linux/amd64 . -t airbyte/source-hubspot-custom


docker buildx build --platform linux/amd64 -f ./Dockerfile -t airbyte/source-hubspot-custom . --output type=docker 
docker tag airbyte/source-hubspot-custom europe-west1-docker.pkg.dev/ps-data-prestashop-airbyte/source-hubspot-custom/v0:latest
docker push europe-west1-docker.pkg.dev/ps-data-prestashop-airbyte/source-hubspot-custom/v0:latest



gcloud builds submit --tag europe-west1-docker.pkg.dev/ps-data-prestashop-airbyte/source-hubspot-custom/v0:latest .
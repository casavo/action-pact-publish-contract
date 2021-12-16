# Pact Publish Contract Action

This action uses [pact-cli](https://github.com/pact-foundation/pact-ruby-cli) 
docker image to perform an opinionated "can I deploy" check:

```console
$ pact broker publish <PACT_OR_DIR> --consumer-app-version <COMMIT_SHA> --tag <ENVIRONMENT>
```

## Inputs

Action inputs are (hopefully) kept in sync with relation option of [`publish` command](https://github.com/pact-foundation/pact_broker-client#publish) from `pact-cli`

### `directory` 

**Required** Directory with pact contract files.

### `consumer_app_version`

**Required** The consumer application version.

### `tag`

**Required** Tag name for consumer version. Can be specified only one times

## Environment Variables

Setup environment variable used by `pact-cli`.

### `PACT_BROKER_BASE_URL`

**Required** The base URL of the Pact Broker

### `PACT_BROKER_USERNAME`

**Required** Pact Broker basic auth username

### `PACT_BROKER_PASSWORD`

**Required** Pact Broker basic auth password

## Usage Example

```yml
steps:
  # ...
  - uses: casavo/pact-publish-contract-action@v1
    env:
      PACT_BROKER_BASE_URL: ${{ secrets.PACT_BROKER_BASE_URL }}
      PACT_BROKER_PASSWORD: ${{ secrets.PACT_BROKER_PASSWORD }}
      PACT_BROKER_USERNAME: ${{ secrets.PACT_BROKER_USERNAME }}
    with:
      directory: ${{ github.workspace }}/your/path/to/pacts
      consumer_app_version: ${{ github.sha }}
      tag: staging
  # ...
```

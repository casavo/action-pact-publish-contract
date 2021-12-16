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

See examples below for more info about how actually use in workflow.

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

Being a Docker container action, the [workspace directory path](https://docs.github.com/en/actions/learn-github-actions/environment-variables) for action is
mounted inside docker container run by the action as `/github/workspace`. 

So, use `/github/workspace` as base path in `directory` input value and append the relative path to pact contract directory files.

### Publish after test

```yml
steps:
  # ...
  
  # assume this will put pact files into $GITHUB_WORKSPACE/build/pacts
  - name: consumer contract test
    run: make test-contract-consumer

  - uses: casavo/pact-publish-contract-action@v1
    env:
      PACT_BROKER_BASE_URL: ${{ secrets.PACT_BROKER_BASE_URL }}
      PACT_BROKER_PASSWORD: ${{ secrets.PACT_BROKER_PASSWORD }}
      PACT_BROKER_USERNAME: ${{ secrets.PACT_BROKER_USERNAME }}
    with:
      directory: /github/workspace/build/pacts
      consumer_app_version: ${{ github.sha }}
      tag: staging
```

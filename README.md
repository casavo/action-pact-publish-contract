# Pact Publish Contract Action

> **CAVEATS** v2 is intended to help in [migrating from tag to branches](https://docs.pact.io/pact_broker/branches#migrating-from-tags-to-branches).
> 
> If ensure, use v1 if your pact publish -> verification still need to be performed using tags

This action uses [pact-cli](https://github.com/pact-foundation/pact-ruby-cli) 
docker image to perform an opinionated "publish contract":

```console
$ pact broker publish <PACT_OR_DIR> \
  --consumer-app-version <GIT_COMMIT_SHA> \
  --branch <GIT_BRANCH> \
  --tag <GIT_BRANCH> \
  --no-auto-detect-version-properties
```

## Inputs

Action inputs are (hopefully) kept in sync with relation option of [`publish` command](https://github.com/pact-foundation/pact_broker-client#publish) from `pact-cli`

### `directory` 

**Required** Directory with pact contract files.

See examples below for more info about how actually use in workflow.

### `consumer_app_version`

**Required** The consumer application version.

### `branch`

**Required** Repository branch of the consumer version.

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

  # GitHub Action provides different env var for branch name if on pull request
  - name: Output git branch
    id: git-branch
    run: |
      echo ::set-output name=head_ref::${GITHUB_HEAD_REF#refs/*/}
      echo ::set-output name=ref::${GITHUB_REF#refs/*/}

  - uses: casavo/pact-publish-contract-action@v1
    env:
      PACT_BROKER_BASE_URL: ${{ secrets.PACT_BROKER_BASE_URL }}
      PACT_BROKER_PASSWORD: ${{ secrets.PACT_BROKER_PASSWORD }}
      PACT_BROKER_USERNAME: ${{ secrets.PACT_BROKER_USERNAME }}
    with:
      directory: /github/workspace/build/pacts
      consumer_app_version: ${{ github.sha }}
      branch: ${{ steps.git-branch.outputs.head_ref || steps.git-branch.outputs.ref }}
```

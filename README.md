# Pact Publish Contract Action

This action uses [pact-cli](https://github.com/pact-foundation/pact-ruby-cli) 
docker image to perform an opinionated "can I deploy" check:

```console
$ pact broker publish <PACT_OR_DIR> --consumer-app-version <COMMIT_SHA> --tag <ENVIRONMENT>
```

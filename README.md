# build-docker-and-deploy-to-caprover

This is a GitHub Action which builds a Docker image and deploys it to a `CapRover` application. Step by step instructions for how to setup `CapRover` are at https://alldjango.com/articles/serve-multiple-django-sites-from-one-cloud-server.

## Install

### Add GitHub Action Secrets

This is optional, but it is best practice so that secrets are not in plaintext.

1. Go to https://github.com/USERNAME/REPOSITORY_NAME/settings/secrets/actions
2. Click *New repository secret* and type "CAPROVER_APP_TOKEN" into *Name* and the generated app token from the last step into *Secret*; click *Add secret*
3. Click *New repository secret* and type "CAPROVER_SERVER_URL" into *Name* and the URL for your `CapRover` server into *Secret*; click *Add secret*

### Create GitHub Action

1. Go to https://github.com/USERNAME/REPOSITORY_NAME/actions
2. If it is available, click *New workflow*
3. Click *set up a workflow for yourself*
4. Name the workflow "deploy-to-caprover.yml" or something similar
5. Copy the following into your file and make sure to update `YOUR-APP-NAME`:
```yaml
on: [push]

jobs:
  deploy_to_caprover:
    runs-on: ubuntu-latest
    name: Deploy to CapRover
    steps:
      - uses: actions/checkout@v3
      - uses: adamghill/build-docker-and-deploy-to-caprover@v1
        with:
          caprover-app-name: YOUR-APP-NAME
          github-token: ${{ secrets.GITHUB_TOKEN }}
          caprover-server-url: ${{ secrets.CAPROVER_SERVER_URL }}
          caprover-app-token: ${{ secrets.CAPROVER_APP_TOKEN }}
```
6. Click *Commit changes...*
7. Click the *Actions` sub-navigation to see the workflow run

## Inputs

These are all of the inputs that are available for this GitHub Action.

### caprover-app-name

The name of the application in CapRover. Required.

### github-token

The GitHub personal access token. Required.

### caprover-server-url

The CapRover server URL. Required.

### caprover-app-token

The CapRover app token. Required.

### branch-name

The name of the branch to build and deploy. Optional and defaults to the current branch.

### docker-file-name

The name of the Docker file to build. Optional and defaults to "./Dockerfile".

### docker-registry

The URL of the Docker registry. Optional and defaults to "ghcr.io".

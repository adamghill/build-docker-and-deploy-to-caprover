# build-docker-and-deploy-to-caprover

This is a GitHub Action which builds a Docker image and deploys it to a `CapRover` application. [`CapRover`](https://caprover.com/) is a free and open source PaaS (platform as a service) that you can run on your own cloud server. I use it on a [DigitalOcean](https://m.do.co/c/617d629f56c0) droplet and it works great for hosting multiple sites cheaply. Step by step instructions for how to setup `CapRover` for Django specifically are at https://alldjango.com/articles/serve-multiple-django-sites-from-one-cloud-server, but can be modified for other web frameworks.

[![DigitalOcean Referral Badge](https://web-platforms.sfo2.digitaloceanspaces.com/WWW/Badge%203.svg)](https://www.digitalocean.com/?refcode=617d629f56c0&utm_campaign=Referral_Invite&utm_medium=Referral_Program&utm_source=badge)

## Install

### Add GitHub Action Secrets

This is optional, but it is best practice so that secrets are not in plain text.

1. Go to https://github.com/USERNAME/REPOSITORY_NAME/settings/secrets/actions
2. Click *New repository secret* and type "CAPROVER_APP_TOKEN" into *Name* and the generated app token from `CapRover` into *Secret*; click *Add secret*
3. Click *New repository secret* and type "CAPROVER_SERVER_URL" into *Name* and the URL for your `CapRover` server into *Secret*; click *Add secret*

### Create GitHub Action

1. Go to https://github.com/USERNAME/REPOSITORY_NAME/actions
2. If it is available, click *New workflow*
3. Click *set up a workflow for yourself*
4. Name the workflow "deploy-to-caprover.yml" or something similar
5. Copy the following into the YAML file and make sure to update `YOUR-APP-NAME`:
6. 
```yaml
on:
  push:
    branches: ["main"]
  workflow_dispatch:

jobs:
  deploy-to-caprover:
    name: Deploy to CapRover
    runs-on: ubuntu-latest

    permissions:
      contents: read
      packages: write

    steps:
      - uses: adamghill/build-docker-and-deploy-to-caprover@main
        with:
          github-token: ${{ secrets.GITHUB_TOKEN }}
          caprover-app-name: YOUR-APP-NAME
          caprover-server-url: ${{ secrets.CAPROVER_SERVER_URL }}
          caprover-app-token: ${{ secrets.CAPROVER_APP_TOKEN }}
```
6. Click *Commit changes...*
7. Click the *Actions* sub-navigation to see the workflow run

## Inputs

These are all of the inputs that are available for the `build-docker-and-deploy-to-caprover` GitHub Action.

### github-token

The GitHub personal access token. Required. Set by the GitHub action automatically.

### caprover-app-name

The name of the application in CapRover. Required.

### caprover-server-url

The CapRover server URL. Required.

### caprover-app-token

The CapRover app token. Required.

### image-name:

The name of the docker image to build. Optional. Defaults to "{github.repository}-{inputs.caprover-app-name}".

To ensure the default image name is valid, the values of `github.repository` and `inputs.caprover-app-name` are slugified using the following shell command:

```shell
echo value | sed -e 's/[^[:alnum:]]/-/g' | tr -s '-' | tr A-Z a-z
```

- `sed -e 's/[^[:alnum:]]/-/g'`: This replaces any character that is not a letter or a number with a dash.
- `tr -s '-'`: This replaces multiple consecutive dashes with a single dash.
- `tr A-Z a-z`: This converts all uppercase letters to lowercase.

### branch-name

The name of the branch to build and deploy. Optional. Defaults to the current branch.

### docker-file-name

The name of the Docker file to build. Optional. Defaults to "./Dockerfile".

### registry

The name of the registry. Optional. Defaults to "ghcr.io".

## Tips

### Low resources on GitHub runner

If you encounter an error suggesting that the runner is low on resources while building the docker image, you can add the step below to free up space on the host machine:

```yaml
- name: Delete unnecessary large tools folder
  run: rm -rf /opt/hostedtoolcache
```

### Unauthorized error message on `CapRover`

If you are using this action on a private repository, the image/package built will automatically be private as well. As a result, you may see an `unauthorized` error message when `CapRover` tries to pull the image.

To resolve this issue, you must configure your `CapRover` instance to access the GitHub registry with the appropriate credentials. Navigate to the *Cluster* section in the left sidebar in your `CapRover` admin site. Here, find an *Add a Remote Registry* button. Click this button and update the form:

- *Username*: Your GitHub username
- *Password*: Generate a [classic GitHub token](https://github.com/settings/tokens/new) with the `read:packages` scope; this is the only required scope for it to work
- *Domain*: ghcr.io
- *Image Prefix*: Leave this blank

After saving the form, the `CapRover` instance can now read all packages (and Docker images) in your GitHub account.

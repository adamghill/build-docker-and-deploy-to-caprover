# build-docker-and-deploy-to-caprover

A GitHub Action which builds a Docker image and deploys to a CapRover application. Step by step instructions for how to setup CapRover for this action are at https://alldjango.com/articles/serve-multiple-django-sites-from-one-cloud-server#github-action-builds-the-docker-image.

## Inputs

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

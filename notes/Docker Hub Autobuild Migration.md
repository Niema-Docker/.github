# Docker Hub Autobuild Migration
My Docker containers have been built in the cloud using [Docker Hub](https://hub.docker.com/)'s [automated build](https://docs.docker.com/docker-hub/repos/manage/builds/) system. However, [Docker Hub Automated Builds have been deprecated and will be fully retired on April 1, 2027](https://docs.docker.com/docker-hub/repos/manage/builds/migrate/). This document contains my notes for migrating all of my Docker containers to build in the cloud using [GitHub Actions](https://github.com/features/actions) instead. These notes are primarily based on Docker's ["Migrate from Autobuilds"](https://docs.docker.com/docker-hub/repos/manage/builds/migrate/) documentation.

# Step 0: Create Docker Hub Access Token
All of my Docker containers are in my personal [niemasd](https://hub.docker.com/u/niemasd) Docker Hub account, so I need to create a [Personal Access Token](https://docs.docker.com/security/access-tokens/personal-access-tokens/). At the beginning of this migration effort, I [generated a new Personal Access Token](https://app.docker.com/accounts/niemasd/settings/personal-access-tokens/create) with **Read & Write** permissions, and I saved it in my password manager's Docker credentials as: "Access Token: GitHub Action Autobuild"

This step does not need to be repeated ever again: I should use this same Personal Access Token for all of my Docker containers.

# Step 1: Create GitHub Action Workflow
I need to create an autobuild GitHub Actions workflow in the GitHub repository currently linked to the Docker container. Docker provides an [example GitHub Actions workflow](https://github.com/docker/autobuilds-actions/blob/main/.github/workflows/simple-build.yaml).

I created one for my [`minimap2` GitHub repository](https://github.com/Niema-Docker/minimap2/blob/main/.github/workflows/dockerhub.yaml) that I should be used as a template for each subsequent repository. The only changes that should be needed are the following:

* If the main branch of the GitHub repository is anything other than `main` (e.g. `master`), I need to change the branch listed in `on: push: branches:` from `main` to that branch name.
* If the versioned release tags are named anything other than `*.*` (e.g. my tools are typically `*.*.*` or `v*.*.*`), I need to change the tag pattern listed in `on: push: tags:` from `"*.*"` to whatever version number pattern the tool (and thus GitHub repository) uses.
* I need to change `env: DOCKER_REPOSITORY_NAME:` to the name of the Docker Hub repository.

# Docker Hub Autobuild Migration
My Docker containers have been built in the cloud using [Docker Hub](https://hub.docker.com/)'s [automated build](https://docs.docker.com/docker-hub/repos/manage/builds/) system. However, [Docker Hub Automated Builds have been deprecated and will be fully retired on April 1, 2027](https://docs.docker.com/docker-hub/repos/manage/builds/migrate/). This document contains my notes for migrating all of my Docker containers to build in the cloud using [GitHub Actions](https://github.com/features/actions) instead. These notes are primarily based on Docker's ["Migrate from Autobuilds"](https://docs.docker.com/docker-hub/repos/manage/builds/migrate/) documentation.

# Step 0: Create Docker Hub Access Token
All of my Docker containers are in my personal [niemasd](https://hub.docker.com/u/niemasd) Docker Hub account, so I need to create a [Personal Access Token](https://docs.docker.com/security/access-tokens/personal-access-tokens/). At the beginning of this migration effort, I [generated a new Personal Access Token](https://app.docker.com/accounts/niemasd/settings/personal-access-tokens/create) with **Read & Write** permissions, and I saved it in my password manager's Docker credentials as: "Access Token: GitHub Action Autobuild"

This step does not need to be repeated ever again: I should use this same Personal Access Token for all of my Docker containers.

# Step 1: Add Docker Hub Access Token to GitHub Repository
I need to configure the GitHub repository linked to the Docker container so that it can use the Personal Access Token I created in Step 0:

1. Go to the "Settings" tab of the GitHub repository (`https://github.com/<USER_OR_ORG>/<REPO_NAME>/settings`).
2. Expand the "Secrets and variables" accordion in the left menu bar (under "Security and quality").
3. Click "Actions" (`https://github.com/<USER_OR_ORG>/<REPO_NAME>/settings/secrets/actions`).
4. Click "New repository secret" (`https://github.com/<USER_OR_ORG>/<REPO_NAME>/settings/secrets/actions/new`).
    * **I can jump straight to the URL in bullet 4, skipping bullets 1-3, for convenience.**
5. Create the following 2 repository secrets:
    * "Name" = `DOCKER_USERNAME` and "Secret" = `niemasd` (my Docker username)
    * "Name" = `DOCKER_TOKEN` and "Secret" = my Docker Hub Personal Access Token

# Step 2: Create GitHub Action Workflow
I need to create an autobuild GitHub Actions workflow in the GitHub repository linked to the Docker container. Docker provides an [example GitHub Actions workflow](https://github.com/docker/autobuilds-actions/blob/main/.github/workflows/simple-build.yaml). I created one for my Minimap2 GitHub repository ([`.github/workflows/dockerhub.yml`](https://github.com/Niema-Docker/minimap2/blob/main/.github/workflows/dockerhub.yml)) that I should be used as a template for each subsequent repository. The only changes that should be needed are the following:

* If the main branch of the GitHub repository is anything other than `main` (e.g. `master`), I need to change the branch listed in `on: push: branches:` from `main` to that branch name.
* If the versioned release tags are named anything other than `*.*`, I need to change the tag pattern listed in `on: push: tags:` from `"*.*"` to whatever version tag pattern the GitHub repository uses.
* I need to change `env: DOCKER_REPOSITORY_NAME:` to the name of the Docker Hub repository.

# Step 3: Delete Docker Hub Build Configuration
Once the GitHub Action has successfully pushed a container to Docker Hub, I need to delete the build configuration from the Docker Hub repository.

1. Go to the Docker Hub repository's "Builds" tab (`https://hub.docker.com/repository/docker/niemasd/<REPO_NAME>/builds`).
2. Click "Configure automated builds" (`https://hub.docker.com/repository/docker/niemasd/<REPO_NAME>/builds/edit`).
    * **I can jump straight to the URL in bullet 2, skipping bullet 1, for convenience.**
3. Click "Delete Build Configuration"

# Summary

1. **Create GitHub repository secrets**
    * URL: `https://github.com/<USER_OR_ORG>/<REPO_NAME>/settings/secrets/actions/new`
    * "Name" = `DOCKER_USERNAME` and "Secret" = `niemasd` (my Docker username)
    * "Name" = `DOCKER_TOKEN` and "Secret" = my Docker Hub Personal Access Token
2. **Create the GitHub Action workflow**
    * URL: `https://github.com/<USER_OR_ORG>/<REPO_NAME>/new/main/.github/workflows`
    * File Name: `dockerhub.yml`
    * Copy the [Minimap2 workflow](https://github.com/Niema-Docker/minimap2/blob/main/.github/workflows/dockerhub.yml) as a template
    * Change `env: DOCKER_REPOSITORY_NAME:` to the name of the Docker Hub repository
    * If the GitHub repository uses a version tag pattern that isn't `*.*`, change `on: push: tags:` to the version tag pattern of the repository
    * If the main branch isn't `main`, change `on: push: branches:` to the name of the main branch (e.g. `master`)
3. **Delete the Docker Hub Build Configuration**
    * URL: `https://hub.docker.com/repository/docker/niemasd/<REPO_NAME>/builds/edit`
    * Click "Delete Build Configuration"

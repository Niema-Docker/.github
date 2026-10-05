# Docker Hub Autobuild Migration
My Docker containers have been built in the cloud using [Docker Hub](https://hub.docker.com/)'s [automated build](https://docs.docker.com/docker-hub/repos/manage/builds/) system. However, [Docker Hub Automated Builds have been deprecated and will be fully retired on April 1, 2027](https://docs.docker.com/docker-hub/repos/manage/builds/migrate/). This document contains my notes for migrating all of my Docker containers to build in the cloud using [GitHub Actions](https://github.com/features/actions) instead. These notes are primarily based on Docker's ["Migrate from Autobuilds"](https://docs.docker.com/docker-hub/repos/manage/builds/migrate/) documentation.

# Step 1: Create Docker Hub Access Tokens
All of my Docker containers are in my personal [niemasd](https://hub.docker.com/u/niemasd) Docker Hub account, so I need to create a [Personal Access Token](https://docs.docker.com/security/access-tokens/personal-access-tokens/).

1. [Generate a new Personal Access Token](https://app.docker.com/accounts/niemasd/settings/personal-access-tokens/create) if necessary.
    * I can check [existing Personal Access Tokens](https://app.docker.com/accounts/niemasd/settings/personal-access-tokens) in my [Docker Account Settings](https://app.docker.com/accounts/niemasd).

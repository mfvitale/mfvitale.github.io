---
layout: post
title:  "Streamlining Your GitHub Actions Workflow with Act"
date:   2023-05-03 11:00:00 +0200
categories: blog
---

Continuous Integration (CI) and Continuous Delivery/Deployment (CD) are essential components of modern software development workflows. They allow developers to build, test, and deploy their applications automatically and quickly, which results in faster feedback cycles, higher quality code, and fewer bugs.

<p align="center">
<img src="{{site.baseurl}}/assets/img/gh-workflow/determinism.png" width="50%">
</p>

Comic by  [geek-and-poke](https://geek-and-poke.com/)

GitHub Actions is a powerful CI/CD platform that enables developers to automate their workflows, from building and testing their code to deploying it to production. One of the limitations of GitHub Actions is that developers can only test their workflows by pushing code to a branch or opening a pull request. However, this can be time-consuming and inefficient, especially when testing complex workflows that involve multiple steps.

That's where [act](https://github.com/nektos/act) comes in. Act is an open-source tool that allows developers to run their GitHub Actions workflows locally.

By running workflows locally, developers can test their code and workflows quickly and efficiently, without having to push code to a branch or open a pull request.

In this article, I will demonstrate how to use act to run a simple workflow that builds a Maven project and caches its dependencies. We will also explain how to use the cache action, which requires the act cache server to be running locally.

# Getting started with act

To get started with act, you first need to install it on your machine. The easiest way to install act is via Homebrew on macOS or Linux. On Windows, you can install act using Chocolatey. See full installation method on [offical docs](https://github.com/nektos/act#installation).

Once you have installed act, you can run it by navigating to your project directory and running the command:

``` bash
act -l
```

This command will list all the available workflows in your project's GitHub Actions configuration file (`.github/workflows/*.yml`). You can then run a specific workflow by specifying its name:

```bash
act -j <job_name>
```

## Building a Maven project with act

Let's assume that you have a Maven project workflow that you want to build and test locally using `act`. Here's an example workflow that you can use:

```yaml
name: Build and Test
on: [push, pull_request]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Cache dependencies
        uses: actions/cache@v2
        with:
          path: ~/.m2/repository
          key: ${{ runner.os }}-maven-${{ hashFiles('**/pom.xml') }}
          restore-keys: |
            ${{ runner.os }}-maven-
      - name: Build and test project
        run: mvn test
```
This workflow consists of a single job named "build" that runs on Ubuntu. The job has three steps:

The first step checks out the code from the repository.
The second step caches the Maven dependencies in the local cache directory (`~/.m2/repository`). This step is important as it can significantly reduce the build time of the project.
The third step builds and tests the project using the mvn test command.

## Using the cache action with act

The `actions/cache` action is an official GitHub Action that allows you to cache files and directories between jobs and workflows. In the example workflow above, we use the cache action to cache the Maven dependencies in the local cache directory.

When using the `actions/cache` action with `act`, you need to have the [act cache server](https://github.com/sp-ricard-valverde/github-act-cache-server) running locally. The cache server is responsible for managing the cache directories and serving cached files when requested by the cache action.

To start the act cache server, you need to have Docker installed on your machine. Once you have Docker installed, you can run the following command in your terminal:

```bash
export ACT_CACHE_AUTH_KEY=foo
```

```bash
docker compose up --build
```

Ensure you add the following configuration to your ~/.actrc file:

```
--env ACTIONS_CACHE_URL=http://localhost:8080/
--env ACTIONS_RUNTIME_URL=http://localhost:8080/
--env ACTIONS_RUNTIME_TOKEN=foo
```

>You can set ACT_CACHE_AUTH_KEY and ACTIONS_RUNTIME_TOKEN to the value you want, but they must be the same.

Once the cache server is running, you can run your workflows locally using act, and the cache action will automatically use the cache server to cache and retrieve files.

```bash
act -j build
```

In summary, act is a powerful tool that allows to run GitHub Actions workflows locally. By using act, you can test your workflows quickly and efficiently, without having to push code to a branch or open a pull request.

Give it a try and see how it can benefit your.

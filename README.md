# Feature Dependencies

Replicates `devcontainers/cli` [issue #163](https://github.com/devcontainers/cli/issues/163).

## Usage

Clone this repository.

```bash
git clone https://github.com/virtualstaticvoid/devcontainers-cli-issue-163.git
```

Open the project in VSCode and then re-open in a development container.

```bash
cd devcontainers-cli-issue-163
code .
```

Or, if you have the `devcontainer` script installed.

```bash
cd devcontainers-cli-issue-163
devcontainer open .
```

Once "inside" the development container, run the tests for `npm-test` feature:

```bash
devcontainer features test . \
  --base-image mcr.microsoft.com/devcontainers/base:ubuntu-22.04 \
  --features npm-test
```

Notice the error reported in output `./install.sh: 4: pip: not found`.

```
...
 => ERROR [dev_containers_target_stage 3/3] RUN cd /tmp/build-features/np  0.4s
------
 > [dev_containers_target_stage 3/3] RUN cd /tmp/build-features/npm-test_1 && set -a && . ./devcontainer-features.env && set +a && chmod +x ./install.sh && ./install.sh:
#0 0.346 Activating feature 'npm-test'
#0 0.347 ./install.sh: 4: pip: not found
------
...
```

<details>
  <summary>Full output</summary>

  ```
  vscode ➜ /workspaces/devcontainers-cli-issue-163 (main ✗) $ devcontainer features test . --base-image mcr.microsoft.com/devcontainers/base:ubuntu-22.04 --features npm-test

  ┌ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ┐
  |    dev container 'features' |
  │           v0.14.2           │
  └ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ┘

  >  baseImage:         mcr.microsoft.com/devcontainers/base:ubuntu-22.04
  >  Target Folder:     /workspaces/devcontainers-cli-issue-163
  >  features:          npm-test
  >  workspaceFolder:   /tmp/vsch/container-features-test/1662982848993

  ⏳ Building test container...

  [1 ms] @devcontainers/cli 0.14.2. Node.js v18.9.0. linux 5.15.0-47-generic x64.
  [37328 ms] Start: Run: docker buildx build --load --build-context dev_containers_feature_content_source=/tmp/vsch-vscode/container-features/0.14.2-1662982849225 --build-arg _DEV_CONTAINERS_BASE_IMAGE=mcr.microsoft.com/devcontainers/base:ubuntu-22.04 --build-arg _DEV_CONTAINERS_IMAGE_USER=root --build-arg _DEV_CONTAINERS_FEATURE_CONTENT_SOURCE=dev_container_feature_content_temp --target dev_containers_target_stage -t vsc-1662982848993-3289bd94f33e17f7e0aa4677032ccb32-features -f /tmp/vsch-vscode/container-features/0.14.2-1662982849225/Dockerfile.extended /tmp/__dev-containers-build-empty
  [+] Building 2.3s (12/12) FINISHED
  => [internal] load build definition from Dockerfile.extended              0.0s
  => => transferring dockerfile: 501B                                       0.0s
  => [internal] load .dockerignore                                          0.0s
  => => transferring context: 2B                                            0.0s
  => resolve image config for docker.io/docker/dockerfile:1.4               0.9s
  => CACHED docker-image://docker.io/docker/dockerfile:1.4@sha256:9ba7531b  0.0s
  => [internal] load .dockerignore                                          0.0s
  => [internal] load build definition from Dockerfile.extended              0.0s
  => [internal] load metadata for mcr.microsoft.com/devcontainers/base:ubu  0.0s
  => [context dev_containers_feature_content_source] load .dockerignore     0.0s
  => => transferring dev_containers_feature_content_source: 2B              0.0s
  => [context dev_containers_feature_content_source] load from client       0.0s
  => => transferring dev_containers_feature_content_source: 2.53kB          0.0s
  => [dev_containers_target_stage 1/3] FROM mcr.microsoft.com/devcontainer  0.2s
  => [dev_containers_target_stage 2/3] COPY --from=dev_containers_feature_  0.0s
  => ERROR [dev_containers_target_stage 3/3] RUN cd /tmp/build-features/np  0.4s
  ------
  > [dev_containers_target_stage 3/3] RUN cd /tmp/build-features/npm-test_1 && set -a && . ./devcontainer-features.env && set +a && chmod +x ./install.sh && ./install.sh:
  #0 0.346 Activating feature 'npm-test'
  #0 0.347 ./install.sh: 4: pip: not found
  ------
  ERROR: failed to solve: executor failed running [/bin/sh -c cd /tmp/build-features/npm-test_1 && set -a && . ./devcontainer-features.env && set +a && chmod +x ./install.sh && ./install.sh]: exit code: 127
  [-] Failed to launch container:

  Command failed: docker buildx build --load --build-context dev_containers_feature_content_source=/tmp/vsch-vscode/container-features/0.14.2-1662982849225 --build-arg _DEV_CONTAINERS_BASE_IMAGE=mcr.microsoft.com/devcontainers/base:ubuntu-22.04 --build-arg _DEV_CONTAINERS_IMAGE_USER=root --build-arg _DEV_CONTAINERS_FEATURE_CONTENT_SOURCE=dev_container_feature_content_temp --target dev_containers_target_stage -t vsc-1662982848993-3289bd94f33e17f7e0aa4677032ccb32-features -f /tmp/vsch-vscode/container-features/0.14.2-1662982849225/Dockerfile.extended /tmp/__dev-containers-build-empty
  ```
</details>

The `npm-test` feature specifies the Python feature in it's [`installsAfter`](./src/npm-test/devcontainer-feature.json#L6-L8), so the expectation is the Python and `pip` will be installed before the feature.

Similarly, the global scenario tests fails too; one explicitly includes the `python` feature and one doesn't.

```bash
devcontainer features test . \
  --base-image mcr.microsoft.com/devcontainers/base:ubuntu-22.04 \
  --global-scenarios-only
```

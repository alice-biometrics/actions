# actions 

<img src="https://github.com/alice-biometrics/custom-emojis/blob/master/images/alice_header.png" width=auto>

Reusable Github Action to help on daily development! 

### GKE Docker Build <img src="https://github.com/alice-biometrics/custom-emojis/blob/master/images/docker.png" width="20">

This action builds an image, checks its vulnerabilities and then publish to a GKE registry. If the image is already published, the workflow will skip unnecessary steps.

*Example:*
```yml
  build:
    uses: alice-biometrics/actions/.github/workflows/gke-docker-build.yml@main
    with:
      image: my-image
      tag: 1.0.0
      dockerfile: Dockerfile
      working_directory: .
    secrets:
      gke_project: ${{ secrets.MY_GKE_PROJECT }}
      gke_key: ${{ secrets.MY_GKE_SERVICE_ACCOUNT_KEY }}
      github_access_token: ${{ secrets.MY_GITHUB_ACCESS_TOKEN }}
```

##### Inputs

| Name                              | Requirement | Default   | Description                                                     |
|-----------------------------------| ----------- |-----------|-----------------------------------------------------------------|
| `image`                           | _required_  |           | Docker Image Name                                               |
| `tag`                             | _required_  |           | Tag or Version of the Docker Image                              |
| `dockerfile`                      | _optional_  | Dockerfile | Dockerfile name                                                 |
| `working_directory`               | _optional_  | .         | Working directory where the docker build will be executed       |
| `additional_tag`                  | _optional_  | latest    | Additional Tag to versioning the docker image                   |
| `publish`                         | _optional_  | true      | If true the workflow will publish tagged images to the registry |
| `update_version_and_deploy_files` | _optional_  | true      | This option update app/VERSION and app/DEPLOY files             |
| `timeout_minutes`                 | _optional_  | 30        | Timeout to cancel the workflow                                  |
| `skip_scan`                       | _optional_  | false     | Allows to skip the scanning of built image                      |
| `pre_build`                       | _optional_  | ''        | Commands to execute before Docker build                         |

##### Outputs

| Name              | Description                                 |
| ----------------- | --------------------------------------------| 
| `build_timestamp` | Timestamp for the time just prior to build. Only valid if input `update_version_and_deploy_files` is true  | 

Example to get this output:

```yml
build:
  uses: alice-biometrics/actions/.github/workflows/gke-docker-build.yml@main
  with:
    image: my-image
    tag: 1.0.0
  secrets:
    gke_project: ${{ secrets.MY_GKE_PROJECT }}
    gke_key: ${{ secrets.MY_GKE_SERVICE_ACCOUNT_KEY }}
    github_access_token: ${{ secrets.MY_GITHUB_ACCESS_TOKEN }}
print-build-timestamp:
    needs: build
    runs-on: ubuntu-latest
    steps:
      - name: Print Build Timestamp
        run: echo "Build Timestamp: ${{ needs.build.build_timestamp }}"
```

##### Secrets

| Name              | Requirement | Description |
| ----------------- | -----------| ----------- |
| `gke_project`  | _required_   | ID of the Google Cloud Platform project. If provided, this will configure `gcloud` to use this project ID by default for commands. Individual commands can still override the project using the `--project` flag which takes precedence. |
| `gke_key`   | _required_  | The service account key which will be used for authentication credentials. This key should be [created](https://cloud.google.com/iam/docs/creating-managing-service-account-keys) and stored as a [secret](https://help.github.com/en/actions/automating-your-workflow-with-github-actions/creating-and-using-encrypted-secrets). It can be encoded as a [Base64](https://en.wikipedia.org/wiki/Base64) string or as JSON. |
| `github_access_token`  | _optional_  | Only required if you need the token to be passed to your dockerfile |  

---

### Helm Deploy <img src="https://github.com/alice-biometrics/custom-emojis/blob/master/images/helm-icon-color.png" width="20">

This action updates, tests and deploys every changes on helm charts
 
*Example:*
```yml
  build:
    uses: alice-biometrics/actions/.github/workflows/helm-deploy.yml@main
    with:
      chart: my-chart
      environment: my-environment
      version: 1.0.0
    secrets:
      gke_project: ${{ secrets.MY_GKE_PROJECT }}
      gke_key: ${{ secrets.MY_GKE_SERVICE_ACCOUNT_KEY }}
      gke_zone: ${{ secrets.MY_GKE_ZONE }}
      gke_cluster_prefix: ${{ secrets.MY_GKE_CLUSTER_PREFIX }}
      github_access_token: ${{ secrets.MY_GITHUB_ACCESS_TOKEN }}
```


##### Inputs

| Name           | Requirement | Default  | Description                                                                      |
| ---------------| ----------- | -------- |----------------------------------------------------------------------------------|
| `chart`        | _required_  |          | Chart to update and then deploy.                                                 |
| `environment`  | _required_  |          | Environment to deploy                                                            |
| `version`      | _required_  | Dockerfile | Image Version to deploy                                                        |
| `test_deploy`  | _optional_  | false    | Test deployment chart using kind.                                                |
| `helm_deploy_args`  | _optional_  | "--wait --timeout 60"  | Add some args to helm deploy command (e.g use `--dry-run` to test your workflow) |


##### Outputs

TODO: It will be nice return some information on failure.

##### Secrets

| Name              | Requirement | Description |
| ----------------- | -----------| ----------- |
| `gke_project`  | _required_   | ID of the Google Cloud Platform project. If provided, this will configure `gcloud` to use this project ID by default for commands. Individual commands can still override the project using the `--project` flag which takes precedence. |
| `gke_key`   | _required_  | The service account key which will be used for authentication credentials. This key should be [created](https://cloud.google.com/iam/docs/creating-managing-service-account-keys) and stored as a [secret](https://help.github.com/en/actions/automating-your-workflow-with-github-actions/creating-and-using-encrypted-secrets). It can be encoded as a [Base64](https://en.wikipedia.org/wiki/Base64) string or as JSON. |
| `gke_zone`  | _required_  | GKE region/zone |  
| `gke_cluster_prefix`  | _required_   | How the cluster name starts (e.g `acme-gke-` ) |  
| `github_access_token`  | _required_  | Github access token to update chart repository |  

---

### Helm Rollback <img src="https://github.com/alice-biometrics/custom-emojis/blob/master/images/helm-icon-color.png" width="20">

This action rollback helm chart backing to previous state and deploy it.
 
*Example:*
```yml
  build:
    uses: alice-biometrics/actions/.github/workflows/helm-rollback.yml@main
    with:
      chart: my-chart
      environment: my-environment
    secrets:
      github_access_token: ${{ secrets.MY_GITHUB_ACCESS_TOKEN }}
```

##### Inputs

| Name           | Requirement | Default    | Description                       |
| ---------------| ----------- | ---------- | --------------------------------- |
| `chart`        | _required_  |            | Chart to update and then deploy.  |
| `environment`  | _required_  |            | Environment to deploy             |


##### Secrets

| Name              | Requirement | Description |
| ----------------- | -----------| ----------- |
| `github_access_token`  | _required_  | Github access token to update chart repository |  

---

### Lume CI 🔥

Performs continuos integrations steps using Python package `lume`. 
This workflow will run the following steps:
  * lint 
  * check-requirements
  * static-analysis
  * test-unit
  * test-integration
  * test-acceptance

*Example:*
```yml
  ci:
    uses: alice-biometrics/actions/.github/workflows/lume-ci.yml@main
    with:
      language: python
```

##### Inputs

| Name                   | Requirement | Default | Description                                                                                                                              |
|------------------------| ----------- |------|------------------------------------------------------------------------------------------------------------------------------------------|
| `language`             | _required_  |      | Select the language (Use python or node)                                                                                                 |
| `lume_version`         | _optional_  | latest | Select the lume version if required. Check the [release history](https://pypi.org/project/lume/#history)                                 |
| `lume_config_filename` | _optional_  | lume.yml | In case you want to change the name of the lume configuration file or just store in another folder                                       |
| `pre_commands`         | _optional_  |  | Set additional lume commands to be executed at the begining of the required ones. Use commas if you need to execute several commands     |
| `post_setup_commands`  | _optional_  |  | Set additional lume commands to be executed after the setup and before required ones. Use commas if you need to execute several commands |
| `post_commands`        | _optional_  |  | Set additional lume commands to be executed at the end of the required ones. Use commas if you need to execute several commands          |


##### Secrets

| Name                   | Requirement | Description                                                                            |
| ---------------------- |-------------| -------------------------------------------------------------------------------------- |
| `github_access_token`  | _optional_  | Only required if you need the token to be passed to requirements or dependency manager | 
| `gke_project`  | _optional_  | ID of the Google Cloud Platform project. If provided, this will configure `gcloud` to use this project ID by default for commands. |
| `gke_key`   | _optional_  | The service account key which will be used for authentication credentials. This key should be [created](https://cloud.google.com/iam/docs/creating-managing-service-account-keys) and stored as a [secret](https://help.github.com/en/actions/automating-your-workflow-with-github-actions/creating-and-using-encrypted-secrets). It can be encoded as a [Base64](https://en.wikipedia.org/wiki/Base64) string or as JSON. |


##### Lume Config File (Required in your repository)

To make it work, it is necessary to have a lume file in the root with at least the following commands (Python example):

```yml
name: Testing

install:
  run: pip install --upgrade --upgrade-strategy eager -r requirements/dev-requirements.txt -r requirements/requirements.txt

steps:
  clean:
    run: echo "clean"
  lint:
    run:
      - black --check .
      - flake8 tests
  check-requirements:
    run: safety check -r requirements/requirements.txt
  test-unit:
    run: echo "test-unit"
  test-integration:
    run: echo "test-integration"
  test-acceptance:
    run: echo "test-acceptance"
```

If you want to change or fix a lume version and also execute additional lume commands, you can do it with the following code:
```yml
  ci:
    uses: alice-biometrics/actions/.github/workflows/lume-ci.yml@main
    with:
      language: python
      lume_version: 0.5.2
      pre_commands: pre-command # should be available on lume.yml otherwise those won't be executed
      post_setup_commands: build # should be available on lume.yml otherwise those won't be executed
      post_commands: my-additional-command-1,my-additional-command-2 # should be available on lume.yml otherwise those won't be executed
```


---

### Notify Slack 💬

This action sends a slack notification to a selected channel.
This actions is a wrapper of the [slack-notify action](https://github.com/marketplace/actions/slack-notify).

*Example:*
```yml
  build:
    uses: alice-biometrics/actions/.github/workflows/notify-slack.yml@main
    with:
      channel: my-slack-channel
      title: Success on Service Deploy
      message: 'New version deployed (v1.1.1)'
    secrets:
      slack_token: ${{ secrets.MY_SLACK_TOKEN }}
```

##### Inputs

| Name        | Requirement | Default      | Description                           |
| ----------- | ----------- | ------------ | ------------------------------------- |
| `channel`   | _required_  |              | Channel to send the message |
| `title`     | _required_  |              | Title to use |
| `message`   | _required_  |              | Message to use|
| `color`     | _optional_  | good (green) | Some options [good, danger, #808080] |
| `icon`      | _optional_  | <img src="https://github.com/alice-biometrics/custom-emojis/blob/master/images/alice_bot.png" width="20">   | Path to a icon image|
| `username`  | _optional_  | Alice Bot    | Slack Username sending the message.  |


##### Secrets

| Name              | Requirement | Description |
| ----------------- | -----------| ----------- |
| `slack_token`     | _required_   | Credentials to access to slack channel |


---

### Get Version 🏷️

This action obtains the version from tags, commits and releases.

*Example:*
```yml
  get-version:
    uses: alice-biometrics/actions/.github/workflows/get-version.yml@main
    with:
      type: commit
```

##### Inputs

| Name        | Requirement | Default | Description                           |
| ----------- | ----------- | ------- | ------------------------------------- |
| `type`      | _required_  |         | Two options [commit, release]. If uses `commit` it returns the version from latest tag and latest commit (e.g `0.0.1-4c5ff17a`). If uses `release` it returns a version obtained from latest tag created on a github release (e.g `0.0.2`)  |


##### Outputs

| Name         | Description                |
| -------------| ---------------------------| 
| `version`    | Version obtained from git  | 

Example to get this output:

```yml
get-version:
  uses: alice-biometrics/actions/.github/workflows/get-version.yml@main
  with:
    type: commit
print-version:
    needs: get-version
    runs-on: ubuntu-latest
    steps:
      - name: Print Version
        run: echo "Version: ${{ needs.get-version.version }}"
```

---

### Commit Changes ✍️

This action modify two files (VERSION, DEPLOY) and push changes to the repository.

*Example:*
```yml
  commit-changes:
    uses: alice-biometrics/actions/.github/workflows/commit-changes.yml@main
    with:
      version: 1.0.0
      deploy_timestamp: 2021-12-02 09:56:29+00:00
    secrets:
      github_access_token: ${{ secrets.MY_GITHUB_ACCESS_TOKEN }}
```


##### Inputs

| Name               | Requirement | Default | Description                                                           |
|--------------------|-------------|-------|-----------------------------------------------------------------------|
| `version`          | _required_  |       | Version of the application                                            |
| `deploy_timestamp` | _required_  |       | Deploy timestamps (you can obtained with `date -u --rfc-3339=seconds`) |
| `working_directory` | _optional_  |       | Working directory                                                     |
| `target_directory` | _optional_  | app   | Target folder where VERSION and DEPLOY files will be stored           |


##### Secrets

| Name                  | Requirement | Description                 |
| --------------------- | ----------- | --------------------------- |
| `github_access_token` | _required_  | Used to push changes to git |  


### Update folder from Git Repository 📁

This action downloads a repository and copy its contain to a defined folder.

*Example:*
```yml
   update-folder-from-git-repository:
    uses: alice-biometrics/actions/.github/workflows/commit-changes.yml@main
    with:
      name: My Workflow Name
      git_repository: github.com/<organization>/<my-repo>.git
      folder: my-folder
      commit_message: Update my-folder
    secrets:
      github_access_token: ${{ secrets.MY_GITHUB_ACCESS_TOKEN }}
```

##### Inputs

| Name              | Requirement  | Description                                                          |
| ----------------- |--------------|----------------------------------------------------------------------|
| `name`            | _required_   | Name of the executed job                                             |
| `git_repository`  | _required_   | Git Repository (format -> github.com/alice-biometrics/<my-repo>.git) |
| `folder`          | _required_   | Folder where repository will be downloaded                           |
| `commit_message`  | _required_   | Message to commit changes if needed                                  |


##### Secrets

| Name                  | Requirement | Description                 |
| --------------------- | ----------- | --------------------------- |
| `github_access_token` | _required_  | Used to push changes to git |  


### Set Global variable 📁

This action sets a global variable, and you can share it across different jobs. 
This solution has been created as a workaround to bypass current GitHub Action limitation.

❌ `The workflow is not valid. .github/workflows/<YOUR-WORKFLOW>.yml (Line: XX, Col: XX): Unrecognized named-value: 'env'. Located at position 1 within expression: env.MY_VAR
`

To bypass this limitation just use set-global action.

*Example:*
```yml
   set-global:
    uses: alice-biometrics/actions/.github/workflows/set-global.yml@main
    with:
      value: My Global Value
```

##### Inputs

| Name    | Requirement  | Description                                           |
|---------|--------------|-------------------------------------------------------|
| `value` | _required_   | Global Value (This will be redirect to the job output |

##### Outputs

| Name    | Description |
|---------|-------------| 
| `value` | Set Value   | 

Example to get this output:

```yml
set-global:
uses: alice-biometrics/actions/.github/workflows/set-global.yml@main
with:
  value: My Global Value
print-version:
    needs: set-global
    runs-on: ubuntu-latest
    steps:
      - name: Print Version
        run: echo "Global: ${{ needs.set-global.value }}"
```

### Publish to PyPI <img src="https://github.com/alice-biometrics/custom-emojis/blob/master/images/python.png" width="16">

This action publish a python package to PyPI registry.

*Example:*
```yml
   publish-pypi:
    uses: alice-biometrics/actions/.github/workflows/publish-pypi.yml@main
    with:
      package_name: my_package
      version: 1.0.0
    secrets:
      username: __token__
      password: pypi-********************
      github_access_token: ${{ secrets.MY_GITHUB_ACCESS_TOKEN }}
```

##### Inputs

| Name           | Requirement  | Description            |
|----------------|--------------|------------------------|
| `package_name` | _required_   | Name of the package    |
| `version`      | _required_   | Version of the package |

##### Secrets

| Name                  | Requirement | Description                                                     |
|-----------------------|-------------|-----------------------------------------------------------------|
| `username`            | _required_  | Username (We use Twine, so default value should be `__token__`) |
| `password`            | _required_  | Password/Token (We use Twine)                                   |
| `github_access_token` | _required_  | Required to upload the VERSION FILE                             | 






Example to get this output:

```yml
set-global:
uses: alice-biometrics/actions/.github/workflows/set-global.yml@main
with:
  value: My Global Value
print-version:
    needs: set-global
    runs-on: ubuntu-latest
    steps:
      - name: Print Version
        run: echo "Global: ${{ needs.set-global.value }}"
```

## Contact :mailbox_with_mail:

support@alicebiometrics.com

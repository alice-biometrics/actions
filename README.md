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

| Name                | Requirement | Default    | Description                                                     |
| ------------------- | ----------- | ---------- | --------------------------------------------------------------- |
| `image`             | _required_  |            | Docker Image Name                                               |
| `tag`               | _required_  |            | Tag or Version of the Docker Image                              |
| `dockerfile`        | _optional_  | Dockerfile | Dockerfile name                                                 |
| `working_directory` | _optional_  | .          | Working directory where the docker build will be executed       |
| `additional_tag`    | _optional_  | latest     | Additional Tag to versioning the docker image                   |
| `publish`           | _optional_  | true       | If true the workflow will publish tagged images to the registry |
| `timeout_minutes`   | _optional_  | 30         | Timeout to cancel the workflow |
| `update_version_and_deploy_files` | _optional_  | true | This option update app/VERSION and app/DEPLOY files |

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
    uses: alice-biometrics/actions/.github/workflows/gke-docker-build.yml@main
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

| Name           | Requirement | Default    | Description                       |
| ---------------| ----------- | ---------- | --------------------------------- |
| `chart`        | _required_  |            | Chart to update and then deploy.  |
| `environment`  | _required_  |            | Environment to deploy             |
| `version`      | _required_  | Dockerfile | Image Version to deploy           |
| `test_deploy`  | _optional_  | false      | Test deployment chart using kind. |


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

| Name                   | Requirement | Default | Description                                                                                                                     |
|------------------------| ----------- |------|---------------------------------------------------------------------------------------------------------------------------------|
| `language`             | _required_  |      | Select the language (Use python or node)                                                                                        |
| `lume_version`         | _optional_  | latest | Select the lume version if required. Check the [release history](https://pypi.org/project/lume/#history)                        |
| `lume_config_filename` | _optional_  | lume.yml | In case you want to change the name of the lume configuration file or just store in another folder                              |
| `additional_commands` | _optional_  |  | Set additional lume commands to be executed at the end of the required ones. Use commas if you need to execute several commands |

##### Secrets

| Name                   | Requirement | Description                                                                            |
| ---------------------- | ----------- | -------------------------------------------------------------------------------------- |
| `github_access_token`  | _optional_  | Only required if you need the token to be passed to requirements or dependency manager | 

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
      additional_commands: my-additional-command-1,my-additional-command-2 # should be available on lume.yml otherwise those won't be executed
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

| Name                | Requirement | Default | Description                 |
| -----------         | ----------- | ------- | --------------------------- |
| `version`           | _required_  |         | Version of the application  |
| `deploy_timestamp`  | _required_  |         | Deploy timestamps (you can obtained with `date -u --rfc-3339=seconds`) |


##### Secrets

| Name                  | Requirement | Description                 |
| --------------------- | ----------- | --------------------------- |
| `github_access_token` | _required_  | Used to push changes to git |  


## Contact :mailbox_with_mail:

support@alicebiometrics.com

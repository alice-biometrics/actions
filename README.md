# actions 

<img src="https://github.com/alice-biometrics/custom-emojis/blob/master/images/alice_header.png" width=auto>

Reusable Github Action to help on daily development! 

### GKE Docker Build <img src="https://github.com/alice-biometrics/custom-emojis/blob/master/images/docker.png" width="20">

This action builds an image, checks its vulnerabilities and then publish to a GKE registry.

Example

```yml
  build:
    uses: alice-biometrics/actions/.github/workflows/gke-docker-build.yml@main
    with:
      image: my-image
      tag: 1.0.0
      dockerfile: Dockerfile
      working_directory: .
    secrets:
      gke_project_id: ${{ secrets.MY_GKE_PROJECT_ID }}
      gke_service_account_key: ${{ secrets.MY_GKE_SERVICE_ACCOUNT_KEY }}
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


##### Secrets

| Name              | Requirement | Description |
| ----------------- | -----------| ----------- |
| `gke_project_id`  | _required_   | ID of the Google Cloud Platform project. If provided, this will configure `gcloud` to use this project ID by default for commands. Individual commands can still override the project using the `--project` flag which takes precedence. |
| `gke_service_account_key`   | _required_  | The service account key which will be used for authentication credentials. This key should be [created](https://cloud.google.com/iam/docs/creating-managing-service-account-keys) and stored as a [secret](https://help.github.com/en/actions/automating-your-workflow-with-github-actions/creating-and-using-encrypted-secrets). It can be encoded as a [Base64](https://en.wikipedia.org/wiki/Base64) string or as JSON. |
| `github_access_token`  | _optional_  | Only required if you need the token to be passed to your dockerfile |  


### CI with Lume 🔥

Performs continuos integrations steps using Python package `lume`. 
This workflow will run the following steps:
  * lint 
  * check-requirements
  * unit tests
  * integration tests
  * acceptance tests

Example

```yml
  ci:
    uses: alice-biometrics/actions/.github/workflows/lume-ci.yml@main
    with:
      language: python
```

##### Inputs

| Name                | Requirement | Default    | Description                                                     |
| ------------------- | ----------- | ---------- | --------------------------------------------------------------- |
| `language`          | _required_  |            | Select the language (Use python or node) Name                                               |
| `lume_config_filename`  | _optional_  | lume.yml | In case you want to change the name of the lume configuration file or just store in another folder |


##### Secrets

| Name              | Requirement | Description |
| ----------------- | -----------| ----------- |
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

## Contact :mailbox_with_mail:

support@alicebiometrics.com

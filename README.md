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

## Contact :mailbox_with_mail:

support@alicebiometrics.com

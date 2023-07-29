# Action Catalogue

These reusable workflows can be composed in any order that fits your project. See the sample action file for [pushing a Docker image](https://github.com/cumberland-cloud/workflows/blob/master/.github/.sample.action.ecr.yaml), [updating a Lambda function](https://github.com/cumberland-cloud/workflows/blob/master/.github/.sample.action.lambda.yaml) or [maintaining a Terraform project](https://github.com/cumberland-cloud/workflows/blob/master/.github/.sample.action.terraform.yaml) for examples of how to compose these workflows.

## Secrets

All reusable workflows, with the exception of the _gh-pages_, _tf-scan_ and _py-lint_ workflows, have the following secrets injected into their execution environment,

| Name | Description | 
| ---- | ----------- |
| AWS_ACCOUNT_ID | Account ID of the pipeline AWS service account |
| AWS_IAM_USER | IAM username of the pipeline AWS service account |
| AWS_ACCESS_KEY_ID | Access key ID of the pipeline AWS service account |
| AWS_SECRET_ACCESS_KEY | Secret access key of the pipeline AWS service account |
| AWS_DEFAULT_REGION | Default region of the pipeline AWS service account |

**Note**: you must ensure all workflows that require these secrets have access to them by allowing the workflow `inherit` the secrets from the repository or organization in which they are set. See [Github Action reusable workflow documentation](https://docs.github.com/en/actions/using-workflows/reusing-workflows#passing-inputs-and-secrets-to-a-reusable-workflow). For example, in order to use the _tf-release_ workflow in a repository where the above secrets are set at the organization level, use the following configuration,

```yaml
name: terraform deploy

on:
  push:
  workflow_dispatch:

jobs:
  Release:
    uses: cumberland-cloud/workflows/.github/workflows/tf-release.yaml@master
    secrets: inherit
    with:
      TF_STATE_KEY: <your-state-key-name>.tfstate
      TF_DEPLOY: true
```

See the _tf-release_ section below for more information on the other parameters nested under `with`.

## ecr-push

[Source](https://github.com/chinchalinchin/github-workflows/blob/main/.github/workflows/ecr-push.yml)

This workflow will build a **Docker** image and then push the image up to an **ECR** in the _Northern Lights_ account.

### Inputs

| Name | Description | Type | Required | 
| ---- | ----------- | ---- | -------- |
| IMAGE_NAME | Name of the image to build | string | true |
| IMAGE_TAG | Tag of the image to build | string | true |
| DOCKERFILE_DIR |  Location of the Dockerfile to build, relative to the repository root directory. Do not include trailing slash. | string | true |
| DOCKER_BUILD_CONTEXT | Location of the Docker build context, relative to the repository root directory. Do not include trailing slash. | string | true|
 
## gh-pages

[Source](https://github.com/chinchalinchin/github-workflows/blob/main/.github/workflows/gh-pages.yml)

This workflow will compile documentation into the `gh-pages` branch of the repository. Based on the files it finds in your repository, it will attempt to construct the documentation through different methods. For example, if your repository has a _.terraform-docs.yml_, it will use `tf-docs` to process the _.tf_ files into _.md_ files.

This workflow uses a **Python** library, [Sphinx](), to transpile _.md_ markdown files into web-hostable _.html_ files. The result of this transpilation is pushed to the `gh-pages` and hosted using the [Github Pages functionality](https://pages.github.com).

### Secrets

This job gets additional, optional secrets injected into its environment.

| Name | Description | Type | Required | Default | 
| ---- | ----------- | ---- | -------- | ------- |
| ACTIONS_BOT_USERNAME| Username of the bot that pushes commits to the "gh-pages" branch | string | true | github-slave-bot |
| ACTIONS_BOT_EMAIL | Email of the bot that pushes commits to the "gh-pages" branch | string | true | slave@github.com |

## lambda-update

[Source](https://github.com/chinchalinchin/github-workflows/blob/main/.github/workflows/lambda-update.yml)

This workflow performs an update on existing **Lambda** function using an **ECR** image and tag. 

### Inputs

| Name | Description | Type | Required | 
| ---- | ----------- | ---- | -------- |
| FUNCTION_NAME | Name of the function to deploy | string | true |
| IMAGE_NAME | Name of the ECR repo where the function's image is hosted | string | true |
| IMAGE_TAG | Tag in the ECR to deploy | string | true |

## py-lint

TODO

## tf-lint

[Source](https://github.com/chinchalinchin/github-workflows/blob/main/.github/workflows/tf-lint.yml)

This workflow runs [tf-lint](https://github.com/terraform-linters/tflint) from the repository's root directory. It requires a _.tflint.hcl_ file to be located in the root directory to configure its execution. See [documentation](https://github.com/terraform-linters/tflint/blob/master/docs/user-guide/config.md) for more information on setting up this file.

## tf-scan

[Source](https://github.com/chinchalinchin/github-workflows/blob/main/.github/workflows/tf-scan.yml)

This workflow runs [tf-sec](https://github.com/aquasecurity/tfsec) and [checkov]() from the repository's root directory. It requires a _.terraform-security.yml_ located in the root directory to configure its execution. See [documentation](https://aquasecurity.github.io/tfsec/v1.28.0/guides/configuration/config/) for more information on setting up this file.

## tf-release

[Source](https://github.com/chinchalinchin/github-workflows/blob/main/.github/workflows/tf-release.yml)

This workflow runs `terraform plan` and `terraform apply` in a succession. Each step is dependent on the success of the previous step. The _terraform.tfvars_ file found in the repository root directory will be passed into each of these commands to provide parameter values. See [documentation](https://developer.hashicorp.com/terraform/language/values/variables#variable-definitions-tfvars-files) for more information on setting up this file.

However, do not add secret information to this file, as it gets committed. Instead, use a [Github Secret](https://docs.github.com/en/rest/actions/secrets). In addition to the _terraform.tfvars_ file, parameter values can also be specified through secret environment variables. See [TF_ENV](./TERRAFORM.md#tf_env) for more information and an example of setting up a new secret.

### Inputs

| Name | Description | Type | Required | 
| ---- | ----------- | ---- | -------- |
| TF_STATE_KEY | Path, including filename, of the Terraform state file to use. | String | Yes |
| TF_APPLY | Path, including filename, of the Terraform state file to use. | String | Yes |


### Secrets

| Name | Description | Type | Required | 
| ---- | ----------- | ---- | -------- |
| TF_ENV | JSON string with key-value pairs representing TF_VAR_* environment variables. | String | No |

# Action Catalogue

These reusable workflows can be composed in any order that fits your project. See the sample action file for [pushing a Docker image](https://github.com/cumberland-cloud/workflows/blob/master/.github/.sample.action.ecr.yaml), [updating a Lambda function](https://github.com/cumberland-cloud/workflows/blob/master/.github/.sample.action.lambda.yaml) or [maintaining a Terraform project](https://github.com/cumberland-cloud/workflows/blob/master/.github/.sample.action.terraform.yaml) for examples of how to compose these workflows.

## ecr-push

[Source](https://github.com/cumberland-cloud/workflows/blob/main/.github/workflows/ecr-push.yaml)

This workflow will build a **Docker** image and then push the image up to an **ECR** in the _Northern Lights_ account.

### Inputs

| Name | Description | Type | Required | 
| ---- | ----------- | ---- | -------- |
| IMAGE_NAME | Name of the image to build | string | true |
| IMAGE_TAG | Tag of the image to build | string | true |
| DOCKERFILE_DIR |  Location of the Dockerfile to build, relative to the repository root directory. Do not include trailing slash. | string | true |
| DOCKER_BUILD_CONTEXT | Location of the Docker build context, relative to the repository root directory. Do not include trailing slash. | string | true|
 
## gh-pages

[Source](https://github.com/cumberland-cloud/workflows/blob/main/.github/workflows/gh-pages.yaml)

This workflow will compile documentation into the `gh-pages` branch of the repository. Based on the files it finds in your repository, it will attempt to construct the documentation through different methods. For example, if your repository has a _.terraform-docs.yml_, it will use [tf-docs](https://terraform-docs.io/) to process the _.tf_ files into _.md_ files.

This workflow uses a **Python** library, [Sphinx](https://www.sphinx-doc.org/en/master/index.html), to transpile _.md_ markdown files into web-hostable _.html_ files. The result of this transpilation is pushed to the `gh-pages` and hosted using the [Github Pages functionality](https://pages.github.com).

### Secrets

This job gets additional, optional secrets injected into its environment.

| Name | Description | Type | Required | Default | 
| ---- | ----------- | ---- | -------- | ------- |
| ACTIONS_BOT_USERNAME| Username of the bot that pushes commits to the "gh-pages" branch | string | true | github-slave-bot |
| ACTIONS_BOT_EMAIL | Email of the bot that pushes commits to the "gh-pages" branch | string | true | slave@github.com |

## lambda-update

[Source](https://github.com/cumberland-cloud/workflows/blob/main/.github/workflows/lambda-update.yaml)

This workflow performs an update on existing **Lambda** function using an **ECR** image and tag. 

### Inputs

| Name | Description | Type | Required | 
| ---- | ----------- | ---- | -------- |
| FUNCTION_NAME | Name of the function to deploy | string | true |
| IMAGE_NAME | Name of the ECR repo where the function's image is hosted | string | true |
| IMAGE_TAG | Tag in the ECR to deploy | string | true |

## py-lint

[Source](https://github.com/cumberland-cloud/workflows/blob/main/.github/workflows/py-lint.yaml)

The workflow lints **Python**
### Inputs

| Name | Description | Type | Required | 
| ---- | ----------- | ---- | -------- |
| SRC_DIR | Path, relative to the repository root directory, where the source code is located. Defaults to the repository root directory. | string | false |
 

## tf-lint

[Source](https://github.com/cumberland-cloud/workflows/blob/main/.github/workflows/tf-lint.yaml)

This workflow runs [tf-lint](https://github.com/terraform-linters/tflint) from the repository's root directory. It requires a _.tflint.hcl_ file to be located in the root directory to configure its execution. See [documentation](https://github.com/terraform-linters/tflint/blob/master/docs/user-guide/config.md) for more information on setting up this file.

## tf-scan

[Source](https://github.com/cumberland-cloud/workflows/blob/main/.github/workflows/tf-scan.yaml)

This workflow runs [tf-sec](https://github.com/aquasecurity/tfsec) and [checkov]() from the repository's root directory. It requires a _.terraform-security.yml_ located in the root directory to configure its execution. See [documentation](https://aquasecurity.github.io/tfsec/v1.28.0/guides/configuration/config/) for more information on setting up this file.

## tf-release

[Source](https://github.com/cumberland-cloud/workflows/blob/main/.github/workflows/tf-release.yml)

This workflow runs `terraform plan` and, if **TF_APPLY** is set to `true`, it will then await for manual approval. If approved, the workflow will resume and input the generated plan file into `terraform apply`. Each step is dependent on the success of the previous step. 

The _terraform.tfvars_ file found in the repository root directory will be passed into each of these commands to provide parameter values. See [documentation](https://developer.hashicorp.com/terraform/language/values/variables#variable-definitions-tfvars-files) for more information on setting up this file.

However, do not add secret information to this file, as it gets committed to version control. Instead, use a [Github Secret](https://docs.github.com/en/rest/actions/secrets). In addition to the _terraform.tfvars_ file, parameter values can also be specified through secret environment variables. See [TF_ENV](./TERRAFORM.md#tf_env) for more information and an example of setting up a new secret.

### Inputs

| Name | Description | Type | Required | 
| ---- | ----------- | ---- | -------- |
| TF_STATE_KEY | Path, including filename, of the Terraform state file to use. | String | Yes |
| TF_APPLY | Path, including filename, of the Terraform state file to use. | String | Yes |


### Secrets

| Name | Description | Type | Required | 
| ---- | ----------- | ---- | -------- |
| TF_ENV | JSON string with key-value pairs representing TF_VAR_* environment variables. | String | No |

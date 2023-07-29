# AWS Workflows

## Workflows

### ecr-push

TODO

### lambda-update

TODO

## Configuration

### Secrets

Each workflow has the following secrets injected into its execution environment,

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
  Push:
    uses: cumberland-cloud/workflows/.github/workflows/ecr-push.yaml@main
    with:
      IMAGE_NAME: "my-image"
      IMAGE_TAG: $${{ github.sha }}
    secrets: inherit
```
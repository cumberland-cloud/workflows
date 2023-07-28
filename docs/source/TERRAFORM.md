# Terraform Workflows

## tf-lint

TODO

## tf-scan

## tf-release

This workflow will run `terraform` directly and generate a [plan file](https://developer.hashicorp.com/terraform/cli/commands/plan) and then apply it.

### IAM Permissions

The [cumberland-cloud org](https://github.com/cumberland-cloud) has an IAM user account with sufficient permissions attached to it that allows it to deploy into the **AWS** cloud. The credentials are stored in organization level secrets and are accessible from every repository under its umbrella. If using the workflow outside of the [cumberland-cloud org](https://github.com/cumberland-cloud), you will need to create [Github repository secrets](https://docs.github.com/en/actions/security-guides/encrypted-secrets) for the [AWS CLI credentials](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-envvars.html).

### TF_ENV

Often, the _terraform.tfvars_ file will contain sensitive information that should not be committed to version control. To avoid exposing this data, the _tf-release_ workflow has been setup to ingest an input variable **TF_ENV**. This is a [Github repository secret](https://docs.github.com/en/actions/security-guides/encrypted-secrets) that gets injected into the workflow. It should be a JSON string containing key-value pairs, where the key corresponds to the name of a **TF_VAR_** environment variable and the value its associated value. See [Terraform Environment Variables](https://developer.hashicorp.com/terraform/cli/config/environment-variables) for more information.

As an example, suppose one of the variables in the _terraform.tfvars_ file was,

```tcl
ec2_ip=123.456.789
```

Rather than storing the variable here and committing it to version control, create a repository secret called **TF_ENV** that looks like the following,

```json
{
    "TF_VAR_ec2_ip": "123.456.789"
}
```

Then the _tf-release_ workflow will automatically pull down the secret (assuming the repository workflow has been given read access to the secret) and use it as input to `terraform plan` and `terraform apply`.
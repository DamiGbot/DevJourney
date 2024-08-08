# Implement and Maintain State

## Terraform State - Defualt Local Backend

Terraform manages your configuration with the use of state files. By default, Terraform uses the local backend, which stores the state file locally in a file called terraform.tfstate.

you can set your backend in the terraform block using the backend block

```hcl
terraform {
    backend "local" {
        path = "terraform.tfstate"
    }
}

```

## Terraform state Locking

Terraform state locking prevents concurrent modifications which could cause corruption. It's important to note that not all backends support state locking.

## Terraform State Backend Storage

The Terraform state file is crucial as it tells Terraform which resources are managed and which resources should be added to your infrastructure. Managing state locally and pushing it to GitHub is not best practice. The state file contains sensitive data, and it is not advisable to store it in a public repository. This is where storage solutions like AWS S3, Azure Blob Storage, and others come in to help manage the state file securely. Terraform configuration can have only a single backend.

## Terraform Enhanced Backend

Enhanced backends store both the configuration and the state files. Examples include local path and Terraform Cloud.

```hcl
terraform {
      backend "remote" {
        hostname = "app.terraform.io"
        organization = "my_orgnaisation"

        workspaces {
            name = "my_workspace"
        }
  }
}
```

## Terraform State Migration

When changing the location of your state storage from local to a standard backend or an enhanced backend, you can migrate your existing state from one location to another.

Use the following command:

```hcl
terraform init -migrate-state
```

## Terraform state refresh

The terraform refresh command checks for changes in your infrastructure and updates the state file to reflect these changes. This command detects drift from the current state but does not keep the changes made outside of Terraform. When you apply the current state, any changes made outside Terraform will be reverted.

```bash
terraform plan -refresh-only
```

or

```bash
terraform apply -refresh-only
```

# Terraform Partial Backend Configuration

Use the following command to initialize Terraform with a partial backend configuration:

```bash
terraform init -backend-config=path_to_file
```

## Sensitive Data in Terraform State

It's important to safely protect your Terraform state as sensitive data can be stored within it. Some backends support encryption, such as Amazon S3 and Terraform Cloud.

### Best Practices for Terraform State

- **Treat State as Sensitive Data**: Protect Terraform state as you would any other sensitive data. State files can contain sensitive information like database passwords.
- **Encrypt State Backend**: Store Terraform state in a backend that supports encryption. Use services like S3, GCS, and Azure Blob Storage that support encryption both in transit and at rest.
- **Control Access to State File**: Strictly control who can access your Terraform backend. Configure IAM policies or equivalent access controls to limit access to trusted individuals or systems.

## Terraform Output

You can output variables using the output block. To list all outputs in your configuration, use:

```bash
terraform output
```

To query a specific output, use:

```bash
terraform output output_name
```

To suppress sensitive data, set the sensitive attribute to true.

## Variable Validation and Suppression

When declaring a variable, validation is crucial. The validation block expects a condition and an error_message to inform the user of any issues.

```hcl
variable "example_variable" {
  type = string

  validation {
    condition     = length(var.example_variable) > 0
    error_message = "The variable must not be empty."
  }
}
```

For sensitive variables, set the sensitive attribute to true to suppress their values.

## Secure Secrets in Terraform Code

It is not advised to store secrets directly in your Terraform configuration as it could be stored in a repository. Use environment variables or secret management tools like HashiCorp Vault to manage secrets securely.

# Use Terrafom Outside of Core Workflow

## Replace Resources using Terraform taint

The terrafom taint command allows for you to manually mark a resource for recreation. to recreate a resource without modifying any terraform configuration. It's a way to force terraform to recreate a resource.

```bash
terraform taint resource_type.resource_name
```

The preferred command is below as the above command is deprecated

```bash
terraform apply --replace="resource_type.resource_name"
```

### Remove the tainted mark from a resource using untaint:

```bash
terraform untaint resource_type.resource_name
```

### Real World Use Case

A resource in AWS has been manually updated in the console or is experiencing a failure that can't be resolved. Restarting the resource can solve this problem without changing the configurations in the Terraform file or updating the state.

## Terraform Import

A scenario could arise where a resource is created outside of Terraform management, but now we want to manage this resource with Terraform. This is where the terraform import command solves the problem.

```bash
terraform import resource_type.resource_name <unique_id>
```

### Example

An EC2 instance has been created manually with an instance ID: i-shifh930-4343435.

First, create a resource block in your Terraform configuration:

```hcl
resource "aws_instance" "example" {}
```

Then, use the import command:

```bash
terraform import aws_instance.example i-shifh930-4343435
```

When you use terraform import to bring an existing resource under Terraform management, Terraform does not create a new instance. Instead, it updates the state file to include a reference to the existing resource, allowing Terraform to manage it going forward

## Terraform Workspaces - OSS

Terraform workspaces allow you to manage Terraform configurations across multiple environments, such as development, staging, and production.

### List Workspaces

```bash
terraform workspace list
```

### Create a new workspace

```bash
terraform workspace new <workspace_name>
```

### Select a workspace

```bash
terraform workspace select <workspace_name>
```

### Show the name of the current workspace

```bash
terraform workspace show
```

### Delete a workspace

```bash
terraform workspace delete <workspace_name>
```

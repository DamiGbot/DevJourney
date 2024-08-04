# Terraform Modules

Terraform modules are sets of resources, configurations, and variables that are packaged and can be reused across your organization. They provide reusable configurations, similar to libraries in conventional programming.

```hcl
module "module_name" {
  source = "put the source of your module"
}

```

## Terrafrom Sources

Terraform modules can be pulled from various sources such as a local path, the Terraform Registry, or GitHub. [for more on terraform source](https://developer.hashicorp.com/terraform/language/modules/sources)

### Inputs and Outputs

Input variables that have no default value are required when calling the module in the main directory:

```hcl
variable "variable_name" {}
```

```hcl
variable "variable_name" {
    default = "default_value"
}
```

Terraform output blocks are used to store valuable information that can be accessed from other parts of your configuration.

```hcl
outputs "ouput_name" {
    value = module.local_path.variable_name
}
```

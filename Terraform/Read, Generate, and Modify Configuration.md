# Read, Generate and Modify Configuration

## Variable Collection and Structure types

When organizing and storing data in Terraform, it comes in various types. Similar to programming languages, Terraform supports data types such as strings, integers, dictionaries (maps), lists (arrays), and more complex types. These data types allow you to define the structure of your input variables clearly and accurately.

### Example

1. String variable type

```hcl
variable "string_example" {
    type: string
    default: "This is the variable"
}
```

2. Number variable type

```hcl
variable "string_example" {
    type: number
    default: 50
}
```

3. Dictionary variable type

```hcl
variable "string_example" {
    type: map(string)
    default: {"key" = "value"}
}
```

4. List (array) variable type

```hcl
variable "string_example" {
    type: list(string)
    default: ["1", "2", "last"]
}
```

5. Complex data type

```hcl
variable "string_example" {
    type: list(object({
        string: {
            innerKey = string
        }
    }))
    default: [{first = {
        innerKey = "innerValue"
    }}]
}
```

_Explanation_: The complex data type allows for nested structures, like lists of objects, which can represent more sophisticated configurations.

## Terraform Built-in Functions

Terraform provides a variety of built-in functions similar to those found in programming languages, such as `min`, `max`, `split`, `join`, `replace`, `substr`, `trim`, and more. These functions help manipulate and process variables within your configurations.

## Dynamic Block

A dynamic block is used to create multiple nested blocks within a single resource. While dynamic blocks are powerful, overusing them can make your configuration difficult to read and maintain. It's recommended to use dynamic blocks only when necessary to simplify the user interface for reusable modules. When possible, write nested blocks explicitly.

```hcl
resource "aws_security_group" "example" {
  dynamic "ingress" {
    for_each = var.ingress_rules
    content {
      from_port   = ingress.value.from_port
      to_port     = ingress.value.to_port
      protocol    = ingress.value.protocol
      cidr_blocks = ingress.value.cidr_blocks
    }
  }
}
```

## Terraform Graph

The `terraform graph` command generates a visual representation of dependencies in your Terraform configuration. This helps you understand the relationships between resources.

```bash
terrofrom graph
```

You can also redirect the output to a `.dot` file and visualize it using tools like Graphviz.

## Resource Meta Arguments

Terraform provides several meta-arguments that can be used to modify the behavior of resources:

- `depends_on`: Explicitly specify dependencies between resources.
- `count`: Create multiple instances of a resource.
- `for_each`: Create resources based on key/value pairs or lists.
- `provider`: Specify which provider configuration to use.
- `lifecycle`: Control resource creation, update, and deletion behaviors (e.g., create_before_destroy, prevent_destroy).

These meta-arguments allow for more granular control over how resources are managed in Terraform.

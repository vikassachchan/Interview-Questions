# Question 1: 

What is the usage of Depends on flag in terraform? 

In Terraform, the depends_on meta-argument is used to specify explicit dependencies between resources. 
It ensures that certain resources are created or destroyed in a specific order relative to other resources.

resource "aws_instance" "example" {
  #Define your instance configuration here
}

resource "aws_eip" "example" {
  #Define your Elastic IP configuration here

  #This EIP resource depends on the creation of the instance
  depends_on = [aws_instance.example]
}

In this example, the Elastic IP resource aws_eip.example depends on the creation of the EC2 instance aws_instance.example. 
This means that Terraform will ensure the instance is created before attempting to create the Elastic IP.

# Question 2:

What is the terraform workspaces and how to switch between workspaces?

Terraform workspaces are a feature that allows you to manage multiple distinct sets of infrastructure resources within
a single configuration. Each workspace maintains its own state, allowing you to manage environments such as development,
staging, and production separately.

To switch between workspaces in Terraform, you can use the `terraform workspace` command. Here's how you can use it:

1. **List Workspaces**: To list all available workspaces, you can use the following command:
   ```
   terraform workspace list
   ```

2. **Create a New Workspace**: If you need to create a new workspace, you can use the following command:
   ```
   terraform workspace new <workspace_name>
   ```

3. **Select a Workspace**: To switch to an existing workspace, use the following command:
   ```
   terraform workspace select <workspace_name>
   ```

4. **Show Current Workspace**: To display the current workspace, you can use the following command:
   ```
   terraform workspace show
   ```

5. **Delete a Workspace**: To delete a workspace, use the following command:
   ```
   terraform workspace delete <workspace_name>
   ```

Remember to use these commands carefully, as they can have implications on your infrastructure state and configurations. 
Always ensure that you are working in the correct workspace before making changes.

# Question 3

What is dynamic block in terraform?

Dynamic blocks in Terraform allow you to define repeating nested blocks dynamically, based on the elements of a list, 
map, or set within your configuration. 
This feature provides flexibility when you need to generate multiple similar blocks with different configurations.

It is used for eg. Ingress blocks where multiple ports are to be allowed. We can use dynamic block to achieve this.

# Question 4

What is the difference between for and for each in terraform?

In Terraform, both `for` and `for_each` are used for looping over elements, but they serve different purposes:

1. **`for`**: The `for` expression is used for generating a sequence of values or transforming one collection into another.
2. It's typically used for generating a list, map, or set of values based on some criteria.

    Example:
    ```hcl
    locals {
      numbers = [1, 2, 3, 4, 5]
      doubled = [for n in local.numbers : n * 2]
    }
    ```

    In this example, `for n in local.numbers : n * 2` generates a new list where each element of `local.numbers` is doubled.

3. **`for_each`**: The `for_each` expression is used in resource or module blocks to
4.  create multiple instances of a resource or module based on a map, set, or list of objects.

    Example:
    ```hcl
    resource "aws_instance" "servers" {
      for_each = {
        web-1 = { ami = "ami-123456", instance_type = "t2.micro" }
        web-2 = { ami = "ami-789012", instance_type = "t2.micro" }
      }

      ami           = each.value.ami
      instance_type = each.value.instance_type
    }
    ```

    In this example, `for_each` creates two instances of the `aws_instance` resource, one for each key-value pair in the map.
    The keys (`web-1`, `web-2`) are used as unique identifiers for each instance, and the values provide the configuration for each instance.

In summary, `for` is used for list comprehension and transformation,
while `for_each` is used for resource or module instantiation with unique identifiers.


# Question 5

Where do we store the tfstate file? and how to enable locking mechanism.

We can store the tfstate file in s3 using the backend. Locking mechanism can be enabled using the dynamodb_table.

```terraform {
  backend "s3" {
    bucket         = "my-terraform-state-bucket"
    key            = "terraform.tfstate"
    region         = "us-east-1"
    encrypt        = true
    dynamodb_table = "terraform-lock"
  }
}
```

# Question 5

Command to destroy only a particular resource.

To destroy only one particular resource using Terraform, you can use the `-target` flag with the `terraform destroy` command. 
This flag allows you to specify the resource you want to destroy without affecting other resources managed by your Terraform configuration.

Here's the syntax:

```bash
terraform destroy -target=resource_type.resource_name
```

Replace `resource_type` with the type of the resource you want to destroy (e.g., `aws_instance`, `aws_s3_bucket`, etc.), 
and `resource_name` with the name of the resource as defined in your Terraform configuration.

For example, if you want to destroy an AWS EC2 instance named `my_instance`, you would run:

```bash
terraform destroy -target=aws_instance.my_instance
```

This command will only destroy the specified resource, leaving other resources managed by your Terraform configuration intact.
However, be cautious when using the `-target` flag, as it can lead to unintended consequences if used improperly.

# Question 6

Command to create only a particular resource.

To apply changes only to a specific resource using Terraform, you can use the `-target` option with the `terraform apply` command. 
This option allows you to specify a single
resource or module to apply changes to, rather than applying changes to all resources in your Terraform configuration.

Here's the syntax:

```bash
terraform apply -target=resource_type.resource_name
```

Replace `resource_type.resource_name` with the resource type and name of the specific resource you want to apply changes to.

For example, if you have an AWS EC2 instance defined in your Terraform configuration as:

```hcl
resource "aws_instance" "example" {
  # Configuration for your EC2 instance
}
```

You can apply changes only to this instance by running:

```bash
terraform apply -target=aws_instance.example
```

This command will apply changes only to the specified EC2 instance, leaving other resources unchanged.

However, please note that using `-target` can have unintended consequences, especially in
complex environments with interdependencies between resources. It's generally recommended to use it with caution and understand its implications on your infrastructure.
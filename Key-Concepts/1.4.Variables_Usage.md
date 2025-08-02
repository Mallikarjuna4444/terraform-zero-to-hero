In Terraform, you can set local environment variables that can be used within the configuration files or in your execution environment. These variables can be defined in several ways, depending on how you want to pass the values to your configuration.

### 1. **Local Variables in Terraform Configuration**

Terraform allows you to define **local variables** within a configuration file using the `locals` block. These variables are specific to the module in which they are defined.

#### Example:

```hcl
locals {
  region = "us-west-2"
  instance_type = "t2.micro"
}

resource "aws_instance" "example" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = local.instance_type
  availability_zone = local.region
}
```

In this example:
- `local.region` and `local.instance_type` are references to the local variables.
- The variables are defined in the `locals` block and used later in the resource block.

### 2. **Environment Variables in Terraform Execution Environment**

You can set environment variables in the system where Terraform is running. These environment variables are often used to configure the behavior of Terraform or to pass sensitive information (like credentials).

#### Example of Setting Environment Variables:

For AWS, for instance, you might set the environment variables like this:

```bash
export AWS_ACCESS_KEY_ID="your_access_key"
export AWS_SECRET_ACCESS_KEY="your_secret_key"
export AWS_DEFAULT_REGION="us-west-2"
```

These environment variables are picked up automatically by Terraform during the execution of commands.

### 3. **Using Environment Variables in Terraform**

Terraform can automatically access environment variables using the `var.<variable_name>` syntax when referencing variables that are set as environment variables.

For example, you can use the following environment variables in a `terraform.tfvars` file or directly in your `main.tf` file:

- `TF_VAR_<variable_name>`: Any environment variable with this prefix will be automatically loaded as a Terraform variable.

```bash
export TF_VAR_region="us-west-2"
export TF_VAR_instance_type="t2.micro"
```

In the Terraform configuration:

```hcl
variable "region" {
  type    = string
  default = "us-west-1"
}

variable "instance_type" {
  type    = string
  default = "t2.medium"
}

resource "aws_instance" "example" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = var.instance_type
  availability_zone = var.region
}
```

### 4. **Passing Variables at Runtime**

You can also pass variables at runtime using the `-var` flag when running Terraform commands. This is useful for setting values on the fly, without hardcoding them into your `.tf` files or environment.

```bash
terraform apply -var "region=us-west-2" -var "instance_type=t2.micro"
```

### 5. **Setting Environment Variables for Sensitive Data**

You can also use environment variables for sensitive information, such as AWS credentials, database passwords, etc., by exporting them before running Terraform commands. This is considered a good practice for security, as sensitive data should not be hardcoded into Terraform files.

For example:

```bash
export TF_VAR_db_password="mysecretpassword"
```

In your `variables.tf` file:

```hcl
variable "db_password" {
  type = string
  sensitive = true
}
```

This will ensure that the password is not displayed in plain text during Terraform runs.

Yes, you're absolutely correct!

### Predefined Environment Variables (AWS credentials and region):
- **AWS_ACCESS_KEY_ID**
- **AWS_SECRET_ACCESS_KEY**
- **AWS_DEFAULT_REGION**

**export ARM_CLIENT_ID="appId"              
export ARM_CLIENT_SECRET="password"          
export ARM_SUBSCRIPTION_ID="<your-subscription-id>"
export ARM_TENANT_ID="tenant"**


These are predefined environment variables used by AWS SDKs, and Terraform (which uses the AWS SDK) automatically picks them up for authentication and configuration purposes without needing any extra setup.

### Custom Variables in Terraform:
For custom variables that you define in your Terraform configuration, such as `region`, `instance_type`, or other user-defined variables, you need to use the `TF_VAR_` prefix in environment variables to pass their values.

### Example:

If you define custom variables in your Terraform configuration, such as:

```hcl
variable "region" {
  type    = string
  default = "us-east-1"
}

variable "instance_type" {
  type    = string
  default = "t2.micro"
}
```

You can set their values using environment variables with the `TF_VAR_` prefix:

```bash
export TF_VAR_region="us-west-2"
export TF_VAR_instance_type="t2.medium"
```

Now, when you run `terraform apply`, Terraform will automatically pick up these custom variable values (since they match the variable names) and use them in your configuration.

### Key Points:
1. **AWS credentials (AWS_ACCESS_KEY_ID, AWS_SECRET_ACCESS_KEY, etc.)** are picked up automatically by Terraform because they are part of the AWS SDK configuration, and you don’t need to use `TF_VAR_` for these specific environment variables.
   
2. For **custom variables** you define in your Terraform configuration, you need to use the `TF_VAR_` prefix in environment variables to pass values to them. For example:
   - Environment variable: `TF_VAR_region="us-west-2"`
   - Variable in Terraform: `variable "region" {}`

This allows you to pass values for variables without needing to hardcode them in the `.tf` files, which is a great way to keep configurations flexible and dynamic.

### Example with both:
```bash
export AWS_ACCESS_KEY_ID="your_access_key"
export AWS_SECRET_ACCESS_KEY="your_secret_key"
export AWS_DEFAULT_REGION="us-west-2"

export ARM_CLIENT_ID="appId"                 # SP application ID
export ARM_CLIENT_SECRET="password"          # SP password
export ARM_SUBSCRIPTION_ID="<your-subscription-id>"
export ARM_TENANT_ID="tenant"


# Custom variables
export TF_VAR_region="us-west-2"
export TF_VAR_instance_type="t2.micro"
```

In this case:
- Terraform will use the AWS credentials from the `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY` environment variables for authentication.
- It will also use the region from `AWS_DEFAULT_REGION` and the custom variable values (`TF_VAR_region` and `TF_VAR_instance_type`) for your defined Terraform variables.

### Summary

- **Local variables** can be defined using the `locals` block in a Terraform configuration file and used within that module.
- **Environment variables** can be set in the shell and used to influence Terraform execution (like AWS credentials).
- **Terraform variables** can be populated by environment variables prefixed with `TF_VAR_`, or passed explicitly with the `-var` flag during command execution.

Would you like more details or examples on a specific use case?

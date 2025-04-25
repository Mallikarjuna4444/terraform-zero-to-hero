Sure! Here's a comprehensive list of commonly used **Terraform commands**, organized by category, with brief explanations. These cover everything from initialization to managing infrastructure:

---

### 🔧 **Setup & Initialization**

| Command | Description |
|--------|-------------|
| `terraform init` | Initializes a Terraform configuration directory (downloads providers, sets up backend, etc.). Must be run before anything else. |

---

### 📋 **Validation & Formatting**

| Command | Description |
|--------|-------------|
| `terraform validate` | Checks if your Terraform configuration is syntactically valid. |
| `terraform fmt` | Formats your `.tf` files to a standard style. |
| `terraform fmt -recursive` | Formats `.tf` files in all subdirectories. |

---

### 📌 **Planning & Applying**

| Command | Description |
|--------|-------------|
| `terraform plan` | Shows the execution plan — what Terraform will do when you run `apply`. |
| `terraform apply` | Applies the changes required to reach the desired state defined in your `.tf` files. |
| `terraform apply -auto-approve` | Applies changes without prompting for confirmation. |
| `terraform plan -var-file=dev.tfvars` | Runs the plan with a specific variable file. |
| `terraform apply -var-file=dev.tfvars` | Applies configuration using a specific variable file. |

---

### 📦 **State Management**

| Command | Description |
|--------|-------------|
| `terraform show` | Shows the current state of resources as tracked in the state file. |
| `terraform state list` | Lists all resources tracked in the current state file. |
| `terraform state show <resource>` | Shows detailed state of a specific resource (e.g., `aws_instance.example`). |
| `terraform state rm <resource>` | Removes a resource from the state file (does not delete it in real life). |
| `terraform state mv <source> <destination>` | Moves a resource in the state file (used during refactoring). |

---

### 🔁 **Resource Importing**

| Command | Description |
|--------|-------------|
| `terraform import <resource_type.name> <resource_id>` | Imports an existing resource into the Terraform state. |
| Example: `terraform import aws_instance.my_instance i-0123456789abcdef0` | Imports an AWS EC2 instance by ID. |

---

### 🧹 **Destroying Resources**

| Command | Description |
|--------|-------------|
| `terraform destroy` | Destroys all infrastructure managed by Terraform. |
| `terraform destroy -auto-approve` | Destroys without asking for confirmation. |
| `terraform destroy -target=<resource>` | Destroys only a specific resource. |

---

### 🧪 **Targeting & Debugging**

| Command | Description |
|--------|-------------|
| `terraform apply -target=<resource>` | Applies only to a specific resource. |
| `TF_LOG=DEBUG terraform apply` | Enables detailed logging (set `TF_LOG` to `INFO`, `DEBUG`, etc.). |

---

### 📁 **Backend & Provider Management**

| Command | Description |
|--------|-------------|
| `terraform providers` | Lists providers used in the configuration. |
| `terraform providers lock` | Generates a provider dependency lock file. |

---

Let me know if you want a cheat sheet or examples for a specific use case (like importing, state manipulation, or deploying a module).

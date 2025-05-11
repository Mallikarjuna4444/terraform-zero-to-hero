Sure! Here's a comprehensive list of commonly used **Terraform commands**, organized by category, with brief explanations. These cover everything from initialization to managing infrastructure:

---
Yes, `terraform init` **does create a `.terraform` folder** in your working directory.

### Here's what happens:

When you run:

```bash
terraform init
```

Terraform initializes the working directory containing your configuration files. As part of this process, it creates a `.terraform/` directory which typically includes:

### Contents of `.terraform/` folder:

| File/Folder                | Description                                                            |
| -------------------------- | ---------------------------------------------------------------------- |
| `.terraform/providers/`    | Contains downloaded provider plugins (e.g., Azure, AWS).               |
| `.terraform.lock.hcl`      | The dependency lock file, specifying exact versions of providers used. |
| `.terraform/modules/`      | Stores downloaded modules (if used).                                   |
| `.terraform/` (root files) | Metadata used to track the state backend, modules, and providers.      |

> 🔒 This folder is internal to Terraform. You typically **shouldn't manually modify** its contents.



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

--------------------------------------------------------------------------------------------------------

Absolutely! Here's a **comprehensive and detailed explanation** for each important **Terraform command**, including what it does, when to use it, and examples. This can act as a mini reference guide or cheat sheet for you.

---

## 🔧 1. **`terraform init`** — *Initialize your Terraform project*

### 📘 Description:
This command sets up your working directory by downloading the required **provider plugins**, initializing the **backend**, and preparing modules.

### ✅ Use When:
- Starting a new Terraform project
- After modifying providers or backend settings
- After adding or changing modules

### 🧪 Example:
```bash
terraform init
```

---

## 📋 2. **Formatting and Validation**

### **`terraform fmt`** — *Format Terraform code*

### 📘 Description:
Automatically rewrites `.tf` files to follow a canonical format (standard indentation, spacing, etc.).

### ✅ Use When:
- You or your team want consistent formatting
- Before committing code to version control

### 🧪 Example:
```bash
terraform fmt
terraform fmt -recursive  # Formats all files in subdirectories
```

---

### **`terraform validate`** — *Validate configuration*

### 📘 Description:
Validates the syntax and internal consistency of the `.tf` files.

### ✅ Use When:
- After writing or editing Terraform code
- As part of CI/CD pipelines

### 🧪 Example:
```bash
terraform validate
```

---

## 📦 3. **Variable Management**

### **`terraform apply -var`** and **`-var-file`**

### 📘 Description:
Allows you to pass variables into the Terraform configuration.

### ✅ Use When:
- You need to override default variables
- You're managing different environments (dev, prod, etc.)

### 🧪 Example (inline variables):
```bash
terraform apply -var="region=us-east-1" -var="instance_type=t2.micro"
```

### 🧪 Example (variable file):
```bash
terraform apply -var-file=dev.tfvars
```

---

## 🔍 4. **Planning and Applying Infrastructure**

### **`terraform plan`** — *Preview changes*

### 📘 Description:
Generates an execution plan showing what Terraform **will do** without making any changes.

### ✅ Use When:
- You want to preview changes before applying
- As part of review/approval in CI/CD pipelines

### 🧪 Example:
```bash
terraform plan
terraform plan -out=tfplan.out  # Save the plan to apply later
```

---

### **`terraform apply`** — *Apply infrastructure changes*

### 📘 Description:
Applies the Terraform configuration and makes the actual changes (create, update, delete resources).

### ✅ Use When:
- You’ve reviewed a plan and are ready to deploy

### 🧪 Example:
```bash
terraform apply
terraform apply -auto-approve  # Skip interactive approval
terraform apply tfplan.out     # Use a saved plan file
```

---

## 🔁 5. **Importing Existing Infrastructure**

### **`terraform import`**

### 📘 Description:
Imports **real-world infrastructure** into Terraform's state so it can be managed by Terraform.

### ✅ Use When:
- You have existing resources (e.g., EC2, S3) and want to start managing them with Terraform.

### 🧪 Example:
```bash
terraform import aws_instance.my_instance i-0123456789abcdef0
```

> 🔥 After importing, use `terraform show` to view the resource and manually write the matching block in `.tf` files.

---

## 📌 6. **Inspecting State**

### **`terraform show`** — *Display current state*

### 📘 Description:
Displays the contents of the current Terraform state in human-readable format.

### ✅ Use When:
- You’ve just imported a resource
- You want to see current values of managed resources

### 🧪 Example:
```bash
terraform show
```

---

### **`terraform state list`** — *List all managed resources*

### 📘 Description:
Lists all resources currently tracked in Terraform state.

### 🧪 Example:
```bash
terraform state list
```

---

### **`terraform state show <resource>`** — *Detailed view of a resource*

### 📘 Description:
Displays all attributes of a single resource from state.

### 🧪 Example:
```bash
terraform state show aws_instance.my_instance
```

---

## 🧹 7. **Modifying State**

### **`terraform state rm`** — *Remove resource from state*

### 📘 Description:
Deletes a resource from Terraform’s state file without destroying the actual infrastructure.

### ✅ Use When:
- You want Terraform to "forget" about a resource

### 🧪 Example:
```bash
terraform state rm aws_instance.my_instance
```

---

### **`terraform state mv`** — *Rename or move a resource in state*

### 📘 Description:
Moves a resource from one name to another in the state file. Useful for refactoring.

### ✅ Use When:
- Renaming resource names or splitting code into modules

### 🧪 Example:
```bash
terraform state mv aws_instance.old_name aws_instance.new_name
```

---

## 💣 8. **Destroying Infrastructure**

### **`terraform destroy`**

### 📘 Description:
Destroys all resources managed by Terraform.

### ✅ Use With Caution:
- When tearing down an environment
- When cleaning up test environments

### 🧪 Example:
```bash
terraform destroy            # Destroys everything
terraform destroy -target=aws_instance.my_instance  # Destroys one resource
terraform destroy -auto-approve  # No confirmation
```

---

## 🛠️ 9. **Provider & Version Info**

### **`terraform providers`** — *List used providers*

### 📘 Description:
Shows all providers used in the configuration, and where each is sourced from.

### 🧪 Example:
```bash
terraform providers
```

---

### **`terraform version`** — *Check Terraform version*

### 📘 Description:
Displays the installed Terraform version and required version for the project.

### 🧪 Example:
```bash
terraform version
```

---

## 🧪 10. **Debugging**

### **`TF_LOG`** — *Set logging level for troubleshooting*

### 📘 Description:
Enables detailed logs when running Terraform commands.

### 🧪 Example:
```bash
TF_LOG=DEBUG terraform apply
```

Levels include: `TRACE`, `DEBUG`, `INFO`, `WARN`, `ERROR`.

---

## ✅ Summary Table (Quick Reference)

| Command | Purpose |
|--------|---------|
| `terraform init` | Set up Terraform working directory |
| `terraform validate` | Validate config syntax |
| `terraform fmt` | Format code |
| `terraform plan` | Preview changes |
| `terraform apply` | Apply changes |
| `terraform destroy` | Destroy resources |
| `terraform show` | Show current state |
| `terraform import` | Import existing resource |
| `terraform state list` | List resources in state |
| `terraform state show` | Show details of a resource |
| `terraform state rm` | Remove resource from state |
| `terraform state mv` | Move/rename state entry |
| `terraform providers` | List used providers |
| `terraform version` | Show Terraform version |

---

If you want, I can turn this into a downloadable PDF cheat sheet or create example `.tf` files for a sample project. Let me know!

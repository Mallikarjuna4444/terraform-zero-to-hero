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

Absolutely! Here's a **more detailed breakdown** of key Terraform commands with **real-world examples**, to help you understand not just what they do, but **how and when to use them**.

---

## 🔧 **1. Initialization**
### `terraform init`
Initializes your Terraform project — it downloads the required provider plugins and sets up the backend if defined.

**Example:**
```bash
terraform init
```

📌 Run this once at the start or any time you change providers or modules.

---

## 📋 **2. Formatting & Validation**
### `terraform fmt`
Automatically formats `.tf` files to follow the standard style.

**Example:**
```bash
terraform fmt
terraform fmt -recursive  # To format all subfolders
```

### `terraform validate`
Checks whether your configuration is syntactically valid.

**Example:**
```bash
terraform validate
```

---

## 📦 **3. Variable Handling**
### `terraform apply -var` and `-var-file`
Pass variables via command line or file.

**Example using individual variables:**
```bash
terraform apply -var="region=us-east-1" -var="instance_type=t2.micro"
```

**Example using variable files:**
```bash
terraform apply -var-file=dev.tfvars
terraform plan -var-file=prod.tfvars
```

---

## 🧪 **4. Plan & Apply**
### `terraform plan`
Shows what changes will be made **without applying them**.

**Example:**
```bash
terraform plan
terraform plan -out=tfplan.out
```

### `terraform apply`
Applies the planned changes to create/update infrastructure.

**Example:**
```bash
terraform apply
terraform apply tfplan.out  # Use saved plan file
```

### `terraform apply -auto-approve`
Skips the prompt and directly applies.

```bash
terraform apply -auto-approve
```

---

## 🔁 **5. Import Existing Resources**
### `terraform import`
Bring existing infrastructure under Terraform management.

**Example:**
```bash
terraform import aws_instance.my_instance i-0123456789abcdef0
```

- This command updates the **state**, not your `.tf` files.
- Then run `terraform show` to get its attributes, and **manually add a matching block** to `main.tf`.

---

## 📌 **6. Inspecting State**
### `terraform show`
Shows the current Terraform state (human-readable format).

```bash
terraform show
```

### `terraform state list`
Lists all resources tracked by Terraform.

```bash
terraform state list
```

### `terraform state show <resource>`
Details a specific resource in the state.

```bash
terraform state show aws_instance.my_instance
```

---

## 🧹 **7. Destroy Resources**
### `terraform destroy`
Tears down infrastructure managed by Terraform.

**Example:**
```bash
terraform destroy  # Destroys everything
terraform destroy -target=aws_instance.my_instance  # Just one resource
terraform destroy -auto-approve  # No confirmation prompt
```

---

## 🛠️ **8. State File Manipulation**
### `terraform state rm`
Removes a resource from the state without deleting it.

**Example:**
```bash
terraform state rm aws_instance.my_instance
```

### `terraform state mv`
Moves/renames a resource within state — useful when refactoring.

**Example:**
```bash
terraform state mv aws_instance.old_name aws_instance.new_name
```

---

## 🧰 **9. Providers & Modules**
### `terraform providers`
Lists providers used in your config.

```bash
terraform providers
```

### `terraform version`
Shows current Terraform version.

```bash
terraform version
```

---

## 🔍 **10. Debugging & Logging**
### `TF_LOG`
Enables detailed logs for debugging.

**Example:**
```bash
TF_LOG=DEBUG terraform apply
```

You can also export it:
```bash
export TF_LOG=TRACE
terraform plan
```

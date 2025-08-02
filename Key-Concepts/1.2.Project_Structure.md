Absolutely! Here's a **clean and scalable Terraform project structure** that uses:

* ✅ A **custom module**
* 🌍 Separate configuration for **multiple environments** (e.g., dev, staging, prod)

---

## 🗂️ Project Structure

```
terraform-project/
├── modules/
│   └── my_module/
│       ├── main.tf
│       ├── variables.tf
│       └── outputs.tf
├── envs/
│   ├── dev/
│   │   ├── main.tf
│   │   ├── variables.tf
│   │   └── terraform.tfvars
│   ├── staging/
│   │   └── ...
│   └── prod/
│       └── ...
├── provider.tf
└── backend.tf
```

---

## 📦 Step-by-Step Setup

### 1. ✅ Custom Module (`modules/my_module/`)

#### `modules/my_module/main.tf`

```hcl
resource "azurerm_resource_group" "rg" {
  name     = var.name
  location = var.location
}
```

#### `modules/my_module/variables.tf`

```hcl
variable "name" {
  type        = string
  description = "Resource group name"
}

variable "location" {
  type        = string
  default     = "East US"
}
```

#### `modules/my_module/outputs.tf`

```hcl
output "resource_group_name" {
  value = azurerm_resource_group.rg.name
}
```

---

### 2. 🌍 Environment Example (`envs/dev/`)

#### `envs/dev/main.tf`

```hcl
module "my_module" {
  source   = "../../modules/my_module"
  name     = var.name
  location = var.location
}
```

#### `envs/dev/variables.tf`

```hcl
variable "name" {
  type = string
}

variable "location" {
  type = string
  default = "East US"
}
```

#### `envs/dev/terraform.tfvars`

```hcl
name     = "rg-dev"
location = "East US"
```

---

### 3. 🌐 Shared Provider (`provider.tf` at root)

```hcl
provider "azurerm" {
  features {}
}
```

---

### 4. 💾 Remote Backend (`backend.tf` at root — optional)

```hcl
terraform {
  backend "azurerm" {
    resource_group_name  = "tfstate-rg"
    storage_account_name = "tfstateaccount"
    container_name       = "tfstate"
    key                  = "dev.terraform.tfstate"
  }
}
```

> You can customize `key = "dev.terraform.tfstate"` per environment.

---

## 🚀 How to Use

From the `envs/dev/` directory:

```bash
terraform init   # Initializes the module and backend
terraform plan   # Previews changes
terraform apply  # Applies infrastructure
```

---

## 🧠 Pro Tips

* Use `terraform.tfvars` for per-env values.
* Use workspaces or remote backend keys to isolate states.
* You can create more reusable modules like `network`, `vm`, `storage`, etc., and combine them in `main.tf`.

---

Would you like this turned into a starter Git repo or add another module like a Virtual Network?

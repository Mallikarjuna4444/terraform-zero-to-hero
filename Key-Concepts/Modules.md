Sure! Here’s a simple example that demonstrates how to create and use a **Terraform module**.

---

### 📁 File Structure:

```
terraform-example/
├── main.tf
├── variables.tf
├── outputs.tf
└── modules/
    └── resource_group/
        ├── main.tf
        ├── variables.tf
        └── outputs.tf
```

---

### 🧩 Module: `modules/resource_group/`

**modules/resource\_group/main.tf**

```hcl
resource "azurerm_resource_group" "this" {
  name     = var.name
  location = var.location
}
```

**modules/resource\_group/variables.tf**

```hcl
variable "name" {
  type        = string
  description = "The name of the resource group"
}

variable "location" {
  type        = string
  description = "Azure location for the resource group"
}
```

**modules/resource\_group/outputs.tf**

```hcl
output "id" {
  value = azurerm_resource_group.this.id
}
```

---

### 📦 Root Module (Caller)

**main.tf**

```hcl
provider "azurerm" {
  features {}
}

module "rg" {
  source   = "./modules/resource_group"
  name     = "example-rg"
  location = "East US"
}
```

**variables.tf**
*(Optional, if you want to make the RG name/location configurable from CLI or tfvars)*

**outputs.tf**

```hcl
output "resource_group_id" {
  value = module.rg.id
}
```

---

### ✅ How to Use It

1. Run:

   ```bash
   terraform init
   terraform apply
   ```

2. Terraform will use the module to create a resource group named `example-rg` in **East US**, and output its ID.

---

Would you like an example of a more advanced module, like one that creates a virtual network or a virtual machine?


To use **existing Terraform cloud modules** (typically from the **Terraform Registry**), you just need to reference them in your configuration using the `source` argument.

---

### ✅ Step-by-Step: Using an Existing Module from Terraform Registry

#### 1. **Find a Module**

Go to [Terraform Registry](https://registry.terraform.io/) and search for a module.
Example: [Azure Resource Group Module](https://registry.terraform.io/modules/Azure/resource-group/azurerm/latest)

---

#### 2. **Use It in Your Code**

**Example** — Using the official Azure resource group module:

```hcl
provider "azurerm" {
  features {}
}

module "rg" {
  source  = "Azure/resource-group/azurerm"
  version = "5.0.0"

  location = "East US"
  name     = "my-rg-from-module"
}
```

> 🔄 Replace `Azure/resource-group/azurerm` and `version` with the module and version you want to use.

---

#### 3. **Initialize the Module**

Run:

```bash
terraform init
```

This downloads the module and its dependencies.

---

#### 4. **Apply the Configuration**

```bash
terraform apply
```

---

### 📝 Notes:

* You can use modules from:

  * Terraform Registry (`source = "namespace/module/provider"`)
  * Git (`source = "git::https://..."`)
  * Local paths (`source = "./modules/..."`)
* Always pin a module version using the `version` argument for stability.

Would you like help choosing a reliable module for a specific Azure service (e.g., VM, VNet, AKS)?

Great — here's a more **advanced Terraform module example** that creates an **Azure Virtual Network (VNet)** along with a **Virtual Machine (VM)** using reusable and configurable module structure.

---

### 📁 Suggested Folder Structure:

```
terraform-advanced-module/
├── main.tf
├── variables.tf
├── outputs.tf
└── modules/
    └── compute/
        ├── main.tf
        ├── variables.tf
        └── outputs.tf
```

---

### 🧩 Module: `modules/compute/` (Reusable)

#### **modules/compute/main.tf**

```hcl
resource "azurerm_network_interface" "nic" {
  name                = "${var.vm_name}-nic"
  location            = var.location
  resource_group_name = var.resource_group_name

  ip_configuration {
    name                          = "internal"
    subnet_id                     = var.subnet_id
    private_ip_address_allocation = "Dynamic"
  }
}

resource "azurerm_virtual_machine" "vm" {
  name                  = var.vm_name
  location              = var.location
  resource_group_name   = var.resource_group_name
  network_interface_ids = [azurerm_network_interface.nic.id]
  vm_size               = var.vm_size

  storage_os_disk {
    name              = "${var.vm_name}-osdisk"
    caching           = "ReadWrite"
    create_option     = "FromImage"
    managed_disk_type = "Standard_LRS"
  }

  storage_image_reference {
    publisher = "Canonical"
    offer     = "UbuntuServer"
    sku       = "18.04-LTS"
    version   = "latest"
  }

  os_profile {
    computer_name  = var.vm_name
    admin_username = var.admin_username
    admin_password = var.admin_password
  }

  os_profile_linux_config {
    disable_password_authentication = false
  }
}
```

#### **modules/compute/variables.tf**

```hcl
variable "vm_name" {}
variable "location" {}
variable "resource_group_name" {}
variable "subnet_id" {}
variable "vm_size" {
  default = "Standard_DS1_v2"
}
variable "admin_username" {}
variable "admin_password" {}
```

#### **modules/compute/outputs.tf**

```hcl
output "vm_id" {
  value = azurerm_virtual_machine.vm.id
}
```

---

### 🧩 Root Configuration: Create RG, VNet, Subnet, and Use VM Module

#### **main.tf**

```hcl
provider "azurerm" {
  features {}
}

resource "azurerm_resource_group" "rg" {
  name     = "example-rg"
  location = "East US"
}

resource "azurerm_virtual_network" "vnet" {
  name                = "example-vnet"
  address_space       = ["10.0.0.0/16"]
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
}

resource "azurerm_subnet" "subnet" {
  name                 = "internal"
  resource_group_name  = azurerm_resource_group.rg.name
  virtual_network_name = azurerm_virtual_network.vnet.name
  address_prefixes     = ["10.0.1.0/24"]
}

module "vm" {
  source              = "./modules/compute"
  vm_name             = "myvm"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  subnet_id           = azurerm_subnet.subnet.id
  admin_username      = "azureuser"
  admin_password      = "P@ssword1234!"  # 🔒 Use a secret or tfvars in production
}
```

---

### ✅ Run the Configuration

```bash
terraform init
terraform plan
terraform apply
```

This setup:

* Creates a resource group, VNet, and subnet.
* Deploys a Linux VM into that subnet via the `compute` module.

---

Would you like this broken into a GitHub repo format or an example using cloud-init to install software on the VM?

Great! Here's a **clean, practical template** showing how to combine:

* `variables.tf` to declare variables
* `terraform.tfvars` to assign values
* A module that consumes those values
* A root `main.tf` to tie it all together

---

### 📁 Folder Structure:

```
terraform-vm-project/
├── main.tf
├── variables.tf
├── terraform.tfvars
└── modules/
    └── compute/
        ├── main.tf
        ├── variables.tf
        └── outputs.tf
```

---

### 🧩 Root Level: `main.tf`

```hcl
provider "azurerm" {
  features {}
}

resource "azurerm_resource_group" "rg" {
  name     = var.resource_group_name
  location = var.location
}

resource "azurerm_virtual_network" "vnet" {
  name                = "example-vnet"
  address_space       = ["10.0.0.0/16"]
  location            = var.location
  resource_group_name = azurerm_resource_group.rg.name
}

resource "azurerm_subnet" "subnet" {
  name                 = "internal"
  resource_group_name  = azurerm_resource_group.rg.name
  virtual_network_name = azurerm_virtual_network.vnet.name
  address_prefixes     = ["10.0.1.0/24"]
}

module "vm" {
  source              = "./modules/compute"
  vm_name             = var.vm_name
  location            = var.location
  resource_group_name = var.resource_group_name
  subnet_id           = azurerm_subnet.subnet.id
  admin_username      = var.admin_username
  admin_password      = var.admin_password
}
```

---

### 🧩 Root Level: `variables.tf`

```hcl
variable "location" {
  type        = string
  description = "Azure region for all resources"
}

variable "resource_group_name" {
  type        = string
  description = "Name of the resource group"
}

variable "vm_name" {
  type        = string
  description = "Name of the virtual machine"
}

variable "admin_username" {
  type        = string
  description = "Admin username for the VM"
}

variable "admin_password" {
  type        = string
  description = "Admin password for the VM"
  sensitive   = true
}
```

---

### 🧾 Root Level: `terraform.tfvars`

```hcl
location            = "East US"
resource_group_name = "demo-rg"
vm_name             = "demo-vm"
admin_username      = "azureuser"
admin_password      = "P@ssw0rd123!"  # ✅ Don't commit this in real projects!
```

---

### 🧩 Module: `modules/compute/`

**`main.tf`**

```hcl
resource "azurerm_network_interface" "nic" {
  name                = "${var.vm_name}-nic"
  location            = var.location
  resource_group_name = var.resource_group_name

  ip_configuration {
    name                          = "internal"
    subnet_id                     = var.subnet_id
    private_ip_address_allocation = "Dynamic"
  }
}

resource "azurerm_virtual_machine" "vm" {
  name                  = var.vm_name
  location              = var.location
  resource_group_name   = var.resource_group_name
  network_interface_ids = [azurerm_network_interface.nic.id]
  vm_size               = var.vm_size

  storage_os_disk {
    name              = "${var.vm_name}-osdisk"
    caching           = "ReadWrite"
    create_option     = "FromImage"
    managed_disk_type = "Standard_LRS"
  }

  storage_image_reference {
    publisher = "Canonical"
    offer     = "UbuntuServer"
    sku       = "18.04-LTS"
    version   = "latest"
  }

  os_profile {
    computer_name  = var.vm_name
    admin_username = var.admin_username
    admin_password = var.admin_password
  }

  os_profile_linux_config {
    disable_password_authentication = false
  }
}
```

**`variables.tf`**

```hcl
variable "vm_name" {}
variable "location" {}
variable "resource_group_name" {}
variable "subnet_id" {}
variable "admin_username" {}
variable "admin_password" {}
variable "vm_size" {
  default = "Standard_DS1_v2"
}
```

**`outputs.tf`**

```hcl
output "vm_id" {
  value = azurerm_virtual_machine.vm.id
}
```

---

### ✅ How to Run

```bash
terraform init
terraform plan
terraform apply
```

This will:

* Read your variable values from `terraform.tfvars`
* Pass them to the module
* Create a VM inside a subnet in a VNet, within a specified RG

---
Excellent question — when your **Terraform module is in a different Azure DevOps (ADO) Git repository**, you can **reference it via a Git source URL** in your `module` block using the `source` argument.

---

### ✅ Step-by-Step: Call a Module from Another Azure DevOps Repo

#### 🧩 1. **Use `source` with Git URL**

You can reference your module like this:

```hcl
module "network" {
  source = "git::https://<organization>@dev.azure.com/<organization>/<project>/_git/<repo-name>//<module-path>?ref=<branch-or-tag>"

  # example input variables
  location            = "East US"
  resource_group_name = "shared-rg"
  vnet_name           = "shared-vnet"
}
```

---

### 🔍 Example

If your module is located in:

* **ADO Project**: `MyProject`
* **Repo Name**: `tf-modules`
* **Path inside repo**: `modules/network`
* **Branch**: `main`

You would write:

```hcl
module "network" {
  source = "git::https://dev.azure.com/myorg/MyProject/_git/tf-modules//modules/network?ref=main"

  location            = "East US"
  resource_group_name = "shared-rg"
  vnet_name           = "shared-vnet"
}
```

> 🧠 `//modules/network` refers to the subdirectory inside the Git repo.
> `?ref=main` pins the module to the `main` branch (you can use a tag, commit hash, etc.).

---

### 🔒 2. **Authentication**

Since Azure DevOps private repos require authentication, you'll need a **Personal Access Token (PAT)**.

You can authenticate in one of these ways:

#### a. **Set Git credentials via environment variable:**

```bash
export GIT_ASKPASS=echo
export GIT_USERNAME=anyvalue
export GIT_PASSWORD=<your-pat>
```

Or use a Git credential manager, or:

#### b. **Embed PAT (not recommended for production)**

```hcl
source = "git::https://<pat>@dev.azure.com/org/project/_git/repo//path?ref=main"
```

⚠️ Avoid hardcoding your PAT — use a secure method like Azure Key Vault or CI pipeline secrets.

---

### ✅ Best Practices

* Use a **tag or commit hash** for versioning (`ref=v1.0.0`).
* Store sensitive info (PATs) in **secure pipeline variables**.
* Keep module paths consistent across repos.

---





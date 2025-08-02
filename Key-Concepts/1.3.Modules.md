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

When your **Terraform module is in a *different Azure DevOps project*** (but within the same organization), you can still use it by referencing the correct Git URL and setting up **secure authentication** — typically with a **Personal Access Token (PAT)**.

Here’s how to securely authenticate and use a Terraform module from a **different Azure DevOps project**:

---

### ✅ 1. **Reference the Module via Git URL**

```hcl
module "example_module" {
  source = "git::https://dev.azure.com/<organization>/<other-project>/_git/<repo-name>//<path-to-module>?ref=<branch-or-tag>"

  # example inputs
  resource_group_name = "example-rg"
  location            = "East US"
}
```

> 🔹 Be sure to replace:
>
> * `<organization>`: Your ADO org name
> * `<other-project>`: The ADO project *where the module is hosted*
> * `<repo-name>`: The module's Git repo name
> * `<path-to-module>`: The subfolder inside the repo (e.g., `modules/vnet`)
> * `<branch-or-tag>`: Recommended to pin to `main` or a version tag like `v1.0.0`

---

### 🔐 2. **Authenticate Using a Personal Access Token (PAT)**

Azure DevOps **requires authentication** for private repos. Here's how to handle it securely:

---

#### 🎯 Option A: **Use Environment Variables in Terraform Workflow**

In your Azure DevOps pipeline or local shell:

```bash
export GIT_TERMINAL_PROMPT=0
export GIT_ASKPASS=echo
export GIT_USERNAME=unused
export GIT_PASSWORD=<your_PAT>
```

Terraform will use these when accessing the Git URL.

---

#### 🎯 Option B: **Use PAT with Token Replacement in CI/CD Pipeline**

In your **Azure DevOps Pipeline YAML**, you can do:

```yaml
variables:
  PAT: $(System.AccessToken)  # or store a custom PAT in pipeline secrets

steps:
  - task: Bash@3
    name: terraformInit
    inputs:
      targetType: 'inline'
      script: |
        export GIT_TERMINAL_PROMPT=0
        export GIT_ASKPASS=echo
        export GIT_USERNAME=unused
        export GIT_PASSWORD=$(PAT)

        terraform init
```

You can also embed the PAT in the `source` URL (less secure, not recommended):

```hcl
source = "git::https://<pat>@dev.azure.com/org/project/_git/repo//modules/vm?ref=main"
```

But you should use this **only in local testing** or behind strong secrets management.

---

### 🔐 PAT Permissions Needed:

Ensure the PAT (or `System.AccessToken`) has:

* **Code > Read & execute** permission on the *project hosting the module repo*.

---

### ✅ Best Practices

* Use **tags** or commit hashes (`ref=v1.0.0`) for version locking.
* **Never commit PATs** to your repo.
* Prefer using **Azure DevOps secrets** or **variable groups** to store PATs securely.

---

Would you like a complete example of an Azure DevOps pipeline that calls a module from another project using a PAT?





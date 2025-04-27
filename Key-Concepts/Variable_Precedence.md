
In Terraform, **variable precedence** (the order of which value Terraform chooses if there are multiple sources) is **very well-defined**.

Here’s the **official precedence order**, from **highest to lowest**:

---

# 🔥 Terraform Variable Precedence (Highest to Lowest)

| Priority | Source                               | Example |
|:--------|:--------------------------------------|:--------|
| 1️⃣ | **-var** flag on CLI when running Terraform | `terraform apply -var="region=us-west-2"` |
| 2️⃣ | **-var-file** flag on CLI (specifying a file) | `terraform apply -var-file="production.tfvars"` |
| 3️⃣ | **Environment variables** (`TF_VAR_` prefixed) | `export TF_VAR_region="us-west-2"` |
| 4️⃣ | **terraform.tfvars** or **terraform.tfvars.json** file (auto-loaded if present) | (automatic load) |
| 5️⃣ | **Any `*.auto.tfvars` or `*.auto.tfvars.json` files** (auto-loaded) | Example: `prod.auto.tfvars` |
| 6️⃣ | **Default value** defined in `variable` block | ```hcl variable "region" { default = "us-east-1" } ``` |

---

# 📜 Full Breakdown:

### 1. **Direct CLI input (`-var`)**
- Highest priority.
- Always overrides everything else.

### 2. **CLI `-var-file` input**
- File passed manually using `-var-file` has higher priority than automatic tfvars loading.

### 3. **Environment Variables (`TF_VAR_*`)**
- Loaded automatically **only if CLI vars are not set**.
- Good for injecting sensitive values (like passwords) securely.

### 4. **terraform.tfvars / terraform.tfvars.json**
- If Terraform sees a `terraform.tfvars` file, it **loads it automatically**.
- If both `.tfvars` and `.tfvars.json` exist, both are loaded, but JSON has slightly later parsing internally.

### 5. **Any `*.auto.tfvars` or `*.auto.tfvars.json`**
- Any file ending in `.auto.tfvars` is automatically loaded **in alphabetical order**.
- Useful for splitting different environment settings (e.g., `dev.auto.tfvars`, `prod.auto.tfvars`).

### 6. **Default value inside `variable` block**
- Lowest priority.
- Used **only** if nothing else provides a value.

---

# 🚀 Visual (Quick Memory Trick)

```
[ -var flag ] > [ -var-file flag ] > [ TF_VAR_ env vars ] > [ terraform.tfvars ] > [ *.auto.tfvars ] > [ variable "default" ]
```

---

# ⚡ Example

Suppose you have:

- `variable "region" { default = "us-east-1" }`
- `terraform.tfvars` setting `region = "us-east-2"`
- An environment variable `export TF_VAR_region="us-west-1"`
- Run: `terraform apply -var="region=us-west-2"`

🛠️ Which one will be used?

✅ **`us-west-2`** because the `-var` flag on CLI **always wins**.

---

# 🎯 Final Tip:
If you ever get weird variable conflicts, **run**:
```bash
terraform console
```
and **type the variable name**, like:
```hcl
> var.region
```
It will show you the **final resolved value**.

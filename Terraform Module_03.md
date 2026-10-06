# Terraform Modules — Fresher Notes

## 1. What is a Terraform Module?

A **module** is a reusable collection of Terraform resources.

Instead of putting all infrastructure in one file:

```text
main.tf
├── VPC
├── Security Group
└── EC2
```

you can organize it:

```text
main.tf
modules/
├── vpc/
├── security-group/
└── ec2/
```

The `main.tf` in the root directory is called the **root module**.

The folders inside `modules/` are **child modules**.

A module is basically a way to **organize and reuse Terraform code**.

---

# 2. Calling a Module

The root module can call a child module:

```hcl
module "vpc" {
  source = "./modules/vpc"
}
```

Meaning:

> Use the Terraform code present in `./modules/vpc`.

---

# 3. Variables

Variables allow a module to **receive input values**.

Inside a module:

```hcl
variable "vpc_cidr" {
  type = string
}
```

The module can use it:

```hcl
cidr_block = var.vpc_cidr
```

The root module can provide the value:

```hcl
module "vpc" {
  source = "./modules/vpc"

  vpc_cidr = "10.0.0.0/16"
}
```

Think:

```text
Root
  │
  │ gives value
  ↓
Module variable
```

### Remember

```text
variable = input to a module
```

---

# 4. Outputs

Outputs allow a module to **expose a value** to its parent/root module.

Inside the module:

```hcl
output "vpc_id" {
  value = aws_vpc.main.id
}
```

Now the root module can access it:

```hcl
module.vpc.vpc_id
```

Think:

```text
Module
  │
  │ exposes value
  ↓
Root
```

### Remember

```text
output = value coming out of a module
```

---

# 5. Module Communication

Modules normally communicate through the **root module**.

For example:

```text
VPC Module
    │
    │ outputs VPC ID
    ↓
Root Module
    │
    │ passes VPC ID
    ↓
Security Group Module
```

### VPC module

```hcl
output "vpc_id" {
  value = aws_vpc.main.id
}
```

### Root module

```hcl
module "security_group" {
  source = "./modules/security-group"

  vpc_id = module.vpc.vpc_id
}
```

### Security Group module

```hcl
variable "vpc_id" {
  type = string
}
```

Then use:

```hcl
vpc_id = var.vpc_id
```

### Simple rule

```text
output   → sends/exposes a value
variable → receives a value
```

---

# 6. `terraform state mv`

`terraform state mv` is useful when you **move an existing resource to a different location in your Terraform configuration**.

For example, originally:

```text
aws_vpc.main
```

Later you move it into a module:

```text
module.vpc.aws_vpc.main
```

Terraform may otherwise think:

> Old resource = deleted  
> New module resource = new resource

To tell Terraform that it is the **same existing resource**, use:

```bash
terraform state mv \
  aws_vpc.main \
  module.vpc.aws_vpc.main
```

This changes the resource's **address in Terraform state**.

It does **not** move the actual AWS resource.

### Important

`terraform state mv`:

- Changes Terraform's state address
- Does not create the resource
- Does not delete the resource
- Helps avoid unnecessary recreation when restructuring configuration

---

# 7. Module Structure

A simple module might look like:

```text
project/
│
├── main.tf
├── variables.tf
├── outputs.tf
│
└── modules/
    └── vpc/
        ├── main.tf
        ├── variables.tf
        └── outputs.tf
```

### Typical flow

```text
Root Module
     │
     │ input
     ↓
Child Module
     │
     │ output
     ↓
Root Module
```

---

# 8. Interview Questions

### What is a Terraform module?

A reusable collection of Terraform resources used to organize and reuse infrastructure code.

### Why use modules?

To improve **reusability, organization, and maintainability** of Terraform configurations.

### What is a variable?

A variable is an **input** that allows values to be passed into a module.

### What is an output?

An output **exposes a value from a module** so the parent/root module can use it.

### How do modules communicate?

The root module can take an output from one module and pass it as an input variable to another module.

### What does `terraform state mv` do?

It changes a resource's address in Terraform state without moving or recreating the actual infrastructure.

---

# Fresher-Level Stop Point

For a beginner DevOps role, know:

- ✅ What a module is
- ✅ Root vs child module
- ✅ `module` block
- ✅ Variables
- ✅ Outputs
- ✅ Passing values between modules
- ✅ Basic module structure
- ✅ `terraform state mv`

You **do not need advanced module design** yet.

**Once you understand the above, move to the next Terraform topic.**

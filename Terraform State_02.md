# Terraform State Basics

* **Terraform code** = What I want
* **State** = What Terraform knows it created (like a log)
* **AWS** = What actually exists

---

## 1. `terraform.tfstate`
Terraform needs a way to remember: *"Which real AWS resource corresponds to which resource in my Terraform code?"*  
That's what `terraform.tfstate` does.

> **Important:**
> * Do **NOT** manually edit `terraform.tfstate`.
> * Do **NOT** commit it to GitHub. Add `terraform.tfstate` and `terraform.tfstate.*` to your `.gitignore` file.

---

## 2. State Locking
State locking prevents multiple Terraform operations from modifying the same state simultaneously, preventing state corruption or conflicting changes.

---

## 3. Remote State and S3 Backend
Remote state and S3 backend are related, but they are not the same thing:

* **Remote state** = The concept: *"Store Terraform's state somewhere other than my laptop."*

```text
              ┌──────────────┐
Developer A ──┤              │
Developer B ──┤ Remote State │
CI/CD ────────┤              │
              └──────────────┘

Terraform State
Terraform code = what I want
State = what Terraform knows it created(like a log)
AWS = what actually exists


terraform.tfstate
Terraform needs a way to remember:
"Which real AWS resource corresponds to which resource in my Terraform code?"
That's what terraform.tfstate does.

Do NOT manually edit terraform.tfstate.
Also, generally don't commit it to GitHub (add terraform.tfstate
terraform.tfstate.* in .gitignore )

3. State Locking
State locking prevents multiple Terraform operations from modifying the same state simultaneously and causing state corruption or conflicting changes.

4. Remote State and S3 Backend
Remote state and S3 backend are related, but they are not the same thing.
Remote state = the concept
"Store Terraform's state somewhere other than my laptop."

              ┌──────────────┐
Developer A ──┤              │
Developer B ──┤ Remote State │
CI/CD ────────┤              │
              └──────────────┘

S3 backend = one specific way to do that in AWS
"Store Terraform's state remotely in an S3 bucket."

terraform {
  backend "s3" {
    bucket = "my-terraform-state"
    key    = "dev/terraform.tfstate"  (path to state file in s3)
    region = "ap-south-1"
  }
}

AWS
│
└── S3 Bucket
      │
      └── terraform.tfstate

"What is remote state?"
Remote state means storing Terraform state in a shared remote location instead of locally, so multiple users or CI/CD systems can work with the same state.

"What is an S3 backend?"
An S3 backend stores Terraform's state remotely in an S3 bucket instead of relying on local state. The key specifies the path where the state file is stored inside the bucket.

6. Drift
Suppose Terraform created:
EC2 instance_type = t3.micro
Then someone goes into AWS Console and manually changes it: t3.medium
But your Terraform code still says:
instance_type = "t3.micro"
AWS actual infrastructure = t3.medium
This difference is called:Infrastructure drift

Interview answer
Drift occurs when the actual infrastructure differs from the configuration or state that Terraform expects, usually because someone changed resources outside Terraform.

7.  terraform refresh
It was used to update Terraform's state based on the current infrastructure. to counter drift changes.
But in Modern Terraform terraform plan can reveal drift by comparing your configuration/state with the actual infrastructure.

8. terraform import
Suppose someone manually created: EC2 instance i-0123456789 in AWS.
But Terraform doesn't know about it.
You can bring that existing resource under Terraform management using: terraform import
terraform import aws_instance.web i-0123456789
it does create any new resources it just brings the existing resource into Terraform's state.

State locking
     ↓
Prevents concurrent state modifications

Remote state
     ↓
Stores state centrally in S3 backend

S3 backend
     ↓
AWS remote state storage

Drift
     ↓
Actual AWS ≠ Terraform expectation

Import
     ↓
Moves existing AWS resource → Terraform management

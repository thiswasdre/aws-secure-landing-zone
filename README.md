# Secure AWS Landing Zone (Terraform)

A foundational **security baseline for an AWS account, defined entirely as code**
with Terraform. It deploys the controls every account should have before running
real workloads — strong identity, audit logging, threat detection, encryption, and
cost guardrails — and tears everything down with a single command.

> Built as a hands-on portfolio project while transitioning into cloud security /
> GRC. Several controls directly remediate misconfigurations I exploited in the
> [flaws.cloud](https://github.com/thiswasdre/AWS---Security---Labs) challenge.

---

## What it deploys

| # | Control | What it does | Why it matters |
|---|---------|--------------|----------------|
| 1 | **IAM password policy** | Enforces strong passwords (14+ chars, symbols, 90-day rotation, no reuse) | Weak/reused passwords are a top breach vector |
| 2 | **Secure S3 bucket** | Block Public Access + AES-256 encryption + versioning | Prevents public-bucket data leaks (flaws.cloud L1–L2) |
| 3 | **CloudTrail** | Multi-region trail with log-file validation → encrypted S3 | "Who did what, when" — core to IR & compliance |
| 4 | **AWS Budgets alarm** | Emails at 80% of a $5/mo budget | Cost guardrail; flags runaway or abused resources |
| 5 | **GuardDuty** | Continuous threat detection on CloudTrail/VPC/DNS logs | Detects credential misuse, recon, and malware |
| 6 | **Least-privilege IAM role** | Read-only "security auditor" role (logs + findings only) | Demonstrates least privilege & the assume-role pattern |

---

## Usage

**Requirements:** Terraform >= 1.5, AWS CLI configured with credentials, and a
`terraform.tfvars` file containing your alert email:

```hcl
alert_email = "you@example.com"
```

**Deploy:**
```bash
terraform init      # download the AWS provider
terraform plan      # preview changes (creates nothing)
terraform apply     # deploy the baseline
```

**Tear down (no lingering costs):**
```bash
terraform destroy
```

---

## Security principles demonstrated
- **Infrastructure as Code** — security controls that are repeatable, reviewable, and version-controlled, not click-ops.
- **Defense in depth** — identity + logging + detection + encryption layered together.
- **Least privilege** — a tightly scoped read-only IAM role (Control 6).
- **Secure by default** — encryption on, public access off, from the very first deploy.
- **Auditability** — CloudTrail with log-file integrity validation.
- **Cost governance** — a budget guardrail baked into the baseline.

## Mapping to real-world lessons
- **Control 2** directly fixes the public / "authenticated-users" S3 exposures from flaws.cloud Levels 1–2.
- **Control 6 (least privilege)** is the discipline that stops the stolen-credential pivot chains seen in Levels 3–5.

## What I learned
- Defining security controls as code is a major shift from clicking in the console — it makes a baseline repeatable, peer-reviewable, and auditable.
- Terraform's `plan` / `apply` / `destroy` workflow lets you stand up and tear down an entire secure baseline safely and at near-zero cost.
- Building in least privilege and secure defaults from the start is far easier than retrofitting them later.

## Notes
- Terraform state and `*.tfvars` are excluded via `.gitignore` — they can contain sensitive data and should never be committed.
- GuardDuty runs under its 30-day free trial; `terraform destroy` removes it along with everything else.

---
*Part of my AWS cloud security portfolio.*

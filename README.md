# Cloud Security Detection Response Lab(AWS)

Hands-on cloud security project that demonstrates **CloudSec + IaC + Incident Simulation**.  
You deploy a small AWS environment, intentionally misconfigure parts to trigger findings, detect them with **GuardDuty** and **CloudTrail**, then **remediate** using hardened Terraform.

> ⚠️ This lab is educational. Do not deploy into production. Keep the Free Tier and set a budget alert.

## What You’ll Show Recruiters
- **Cloud Security**: GuardDuty, CloudTrail, S3 security, IAM hardening, security groups
- **Infrastructure as Code**: Terraform (insecure baseline ➜ secure redeploy)
- **Incident Response**: Alert → Investigate → Contain → Eradicate → Lessons learned

## Architecture (minimal)
- 1× S3 bucket (initially public in `/terraform` to simulate an S3 data exposure)
- 1× EC2 (Amazon Linux 2) with a permissive security group (SSH 0.0.0.0/0) in baseline
- 1× IAM user with overly permissive policy in baseline
- GuardDuty + CloudTrail + AWS Config enabled for monitoring

In `/terraform-secure` you redeploy with:
- S3 Block Public Access + encryption + versioning
- Principle of Least Privilege IAM
- Security group scoped to your IP only
- Same monitoring, plus Config rules to catch drift

## Quickstart

### Prereqs
- Terraform >= 1.6
- AWS account (Free Tier), access key configured via `aws configure` or env vars
- Replace placeholders in `terraform/*.tfvars` (optional) and **never commit secrets**

### Deploy the INSECURE baseline (to generate findings)
```bash
cd terraform
terraform init
terraform plan -out=tfplan
terraform apply -auto-approve
```
Now run the incident simulations in `/incidents`. Check GuardDuty and CloudTrail.

### Harden by redeploying the SECURE version
```bash
cd ../terraform-secure
terraform init
terraform plan -out=tfplan
terraform apply -auto-approve
```
Re-run the detections to confirm the findings are resolved.

## Repo Layout
```
terraform/          # Intentionally insecure lab (for triggering findings)
terraform-secure/   # Hardened redeploy (remediations)
incidents/          # Incident runbooks (S3 public, IAM abuse, EC2 brute force)
detections/         # Sample GuardDuty + CloudTrail artifacts
hardening/          # What changed and why
scripts/            # Helpers (optional)
```

## Notes
- **Budgets**: Create AWS Budgets alerts ($5/10 thresholds) to avoid surprise charges.
- **Cleanup**: `terraform destroy` in both folders when done.
- **Costs**: Designed to stay within Free Tier, but always monitor usage.

## License
MIT https://www.linkedin.com/in/sanskruti-morsaniya 

# LocalStack + Terraform Lab Environment

**Practice AWS Infrastructure on Windows — No AWS Account Required**

5 hands-on labs covering S3, DynamoDB, SQS, Lambda, and VPC. Everything runs locally inside Docker using LocalStack (free v3.8.1).

> No cloud costs. No AWS account. No credentials that expire.

---

## What This Project Is

A complete Terraform practice environment that runs entirely on your local Windows machine. It uses LocalStack — an open-source tool that emulates AWS services inside a Docker container — so you can write and apply real Terraform configurations without touching an actual AWS account.

Designed as preparation for:

- HashiCorp Terraform Associate (004)
- AWS Cloud Practitioner (CLF-C02)
- AWS Solutions Architect Associate (SAA-C03)

---

## Folder Structure

```
scripts-v3-fixed\
│
├── 00-terraform-env-setup.ps1       One-time install of all tools + config
├── 01-start-localstack.ps1          Start LocalStack before every lab session
├── 99-uninstall-everything.ps1      Full cleanup and uninstall when done
│
├── lab-01-s3\
│     01-create-files.ps1
│     02-deploy.ps1
│     03-verify.ps1                  Verifies resources + opens browser
│     04-destroy.ps1
│
├── lab-02-dynamodb\
│     01-create-files.ps1
│     02-deploy.ps1
│     03-verify.ps1                  Insert item + scan table
│     04-destroy.ps1
│
├── lab-03-sqs\
│     01-create-files.ps1
│     02-deploy.ps1
│     03-verify.ps1                  Send + receive a test message
│     04-destroy.ps1
│
├── lab-04-lambda\
│     01-create-files.ps1
│     02-restart-localstack-for-lambda.ps1
│     03-deploy.ps1
│     04-verify.ps1                  Invoke function + read response
│     05-destroy.ps1                 Destroy + remove Lambda runtime image
│
└── lab-05-vpc\
      01-create-files.ps1
      02-deploy.ps1
      03-verify.ps1                  Describe VPCs, subnets, security groups
      04-destroy.ps1
```

---

## Before You Run Any Script

Open PowerShell **as Administrator** and run this once:

```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
```

Type `Y` and press Enter. You only need to do this once.

---

## Scripts — Run in This Order

### Step 1 — Install all tools (run once)

```powershell
.\00-terraform-env-setup.ps1
```

Installs: Docker Desktop, Chocolatey, Terraform, AWS CLI v2, Python, LocalStack CLI, tflocal.

Each tool is skipped automatically if already installed. Estimated time: 5–15 minutes.

> **When complete: RESTART YOUR MACHINE before continuing.**

### Step 2 — After restart, open Docker Desktop

Open Docker Desktop from the Start Menu. Wait for the whale icon in the system tray to stop animating.

Confirm it is running:

```powershell
docker --version
docker ps
```

If Docker did not start automatically:

```powershell
Start-Service com.docker.service
```

### Step 3 — Start LocalStack (run before every lab session)

```powershell
.\01-start-localstack.ps1
```

Choose from the menu:

| Option | Mode | Use for |
| --- | --- | --- |
| `[1]` | Standard Start | Labs 01, 02, 03 |
| `[2]` | Lambda Start | Lab 04 only (mounts Docker socket) |
| `[3]` | Verify | Check if LocalStack is already running |

LocalStack listens on: `http://localhost:4566`

### Step 4 — Run the labs (run scripts in order for each lab)

**Lab 01 — S3 Buckets**

```powershell
cd C:\terraform-labs\lab-01-s3
.\01-create-files.ps1
.\02-deploy.ps1
.\03-verify.ps1        # opens browser at localhost:4566/my-localstack-bucket/index.html
.\04-destroy.ps1
```

**Lab 02 — DynamoDB**

```powershell
cd C:\terraform-labs\lab-02-dynamodb
.\01-create-files.ps1
.\02-deploy.ps1
.\03-verify.ps1
.\04-destroy.ps1
```

**Lab 03 — SQS Message Queues**

```powershell
cd C:\terraform-labs\lab-03-sqs
.\01-create-files.ps1
.\02-deploy.ps1
.\03-verify.ps1
.\04-destroy.ps1
```

**Lab 04 — Lambda Functions**

Run `.\01-start-localstack.ps1` first and choose `[2]` Lambda Start.

```powershell
cd C:\terraform-labs\lab-04-lambda
.\01-create-files.ps1
.\02-restart-localstack-for-lambda.ps1   # only if LocalStack is already running from a previous lab
.\03-deploy.ps1        # Lambda creation takes 30-40 seconds -- normal
.\04-verify.ps1
.\05-destroy.ps1       # also removes the Lambda runtime image ~700 MB
```

**Lab 05 — VPC + Security Groups**

Run `.\01-start-localstack.ps1` and choose `[1]` Standard Start.

```powershell
cd C:\terraform-labs\lab-05-vpc
.\01-create-files.ps1
.\02-deploy.ps1
.\03-verify.ps1
.\04-destroy.ps1
```

### Step 5 — Stop LocalStack when done for the day

```powershell
docker stop localstack
```

### Step 6 — Full cleanup when done with all labs

```powershell
.\99-uninstall-everything.ps1
```

Removes: LocalStack image, Lambda runtime image, Terraform, AWS CLI, LocalStack CLI, tflocal, AWS credentials, `C:\terraform-labs\`, and all `%TEMP%` installer/cache files.

Prompts individually before removing Python, Docker Desktop, Chocolatey. Offers a drive defrag/optimize at the end.

---

## Troubleshooting

| Symptom | Fix |
| --- | --- |
| `script cannot be loaded, not digitally signed` | `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser` |
| `'choco' is not recognized` | Close PowerShell, reopen as Administrator, re-run `00-terraform-env-setup.ps1` |
| `'aws' is not recognized` | Close and reopen PowerShell as Administrator |
| LocalStack connection refused | Docker Desktop is not fully started. Wait for whale icon to stop animating. |
| S3 DNS / host lookup error | `provider.tf` is missing `s3_use_path_style = true`. Re-run `.\01-create-files.ps1` |
| AWS CLI JSON / BOM error on DynamoDB or SQS | Use the scripts as provided — they use `WriteAllText()` to avoid the issue |
| Lambda: Docker not available | Run `.\01-start-localstack.ps1` and choose `[2]` Lambda Start |
| Lambda creation takes a long time | Normal — first deploy pulls `public.ecr.aws/lambda/python:3.11` (~700 MB) |
| `terraform apply: connection refused` | LocalStack is not running — run `.\01-start-localstack.ps1` first |
| Docker images not removed by uninstall script | Start Docker Desktop first, then run the `docker rmi` commands below |

To manually remove Docker images:

```powershell
docker rmi localstack/localstack --force
docker rmi public.ecr.aws/lambda/python:3.11 --force
```

---

## Quick Reference — Commands

**Start LocalStack (standard):**

```powershell
docker run --rm -d -p 4566:4566 --name localstack localstack/localstack:3.8.1
```

**Start LocalStack (Lambda mode):**

```powershell
docker run --rm -d -p 4566:4566 -v /var/run/docker.sock:/var/run/docker.sock --name localstack localstack/localstack:3.8.1
```

**Stop LocalStack:**

```powershell
docker stop localstack
```

**Check LocalStack health:**

```powershell
curl http://localhost:4566/_localstack/health
```

**List resources:**

```powershell
aws --endpoint-url=http://localhost:4566 s3 ls
aws --endpoint-url=http://localhost:4566 dynamodb list-tables
aws --endpoint-url=http://localhost:4566 sqs list-queues
aws --endpoint-url=http://localhost:4566 lambda list-functions
```

**Terraform commands:**

```
terraform init                   Download providers
terraform plan                   Preview changes (dry run)
terraform apply                  Create/update resources
terraform apply -auto-approve    Apply without prompt
terraform destroy                Destroy all resources
terraform destroy -auto-approve  Destroy without prompt
terraform output                 Show output values
terraform state list             List all tracked resources
```

---

## Summary of the Labs Covered

A fully automated local AWS practice environment using Terraform + LocalStack on Windows — no AWS account, no cloud costs, no credentials that expire.

The setup is a single PowerShell script that installs Docker Desktop, Terraform, AWS CLI, Python, and LocalStack, configures everything, and cleans up after itself.

Five hands-on labs walk through the core AWS services you need for the Terraform Associate and AWS certifications:

- 🪣 **Lab 01 — S3:** bucket versioning, encryption, static website hosting
- 🗄️ **Lab 02 — DynamoDB:** NoSQL tables, item insertion, scanning
- 📬 **Lab 03 — SQS:** standard queues, FIFO queues, dead-letter queues
- ⚡ **Lab 04 — Lambda:** Python serverless functions, IAM roles, invocation
- 🌐 **Lab 05 — VPC:** subnets, internet gateway, route tables, security groups

Every lab has numbered scripts that create the Terraform files, deploy, verify, and destroy — so the focus stays on learning the infrastructure patterns, not fighting tooling. Also includes a full uninstall script that removes every tool, Docker image, lab file, and temp cache when you are done.

Built as a free alternative to cloud sandboxes for anyone preparing for HashiCorp Terraform Associate (004), AWS Cloud Practitioner, or AWS Solutions Architect Associate.

---

`#Terraform` `#AWS` `#LocalStack` `#DevOps` `#InfrastructureAsCode` `#CloudCertification` `#HashiCorp`

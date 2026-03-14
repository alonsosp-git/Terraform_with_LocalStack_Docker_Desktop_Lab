What This Project Is — explains LocalStack, that no AWS account is needed, what certifications it prepares for.

Folder Structure — visual tree of every script with a one-line note on what each does.

Before You Run Any Script in Powershell run: Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser 

Scripts steps: 

Step 1 – Setup — exact cd and .\ commands, what each of the 7 install steps does, and the restart requirement.
Step 2 – Docker Desktop after restart — how to confirm it's running, the Start-Service com.docker.service command if it's stopped, and how to verify with docker ps.
Step 3 – Start LocalStack — the menu explained, when to use option 1 vs 2 vs 3, and what port LocalStack listens on.
Step 4 – Labs — each lab with its folder path, what Terraform resources it creates, and a note on anything non-obvious (BOM encoding fix, Lambda 30-40s wait, base64 payload, browser auto-open for Lab 01).
Steps 5 & 6 — stopping LocalStack and full cleanup with the complete list of everything 99-uninstall-everything.ps1 removes including all %TEMP% paths.


Troubleshooting — 10 confirmed errors with their exact fix.
Quick Reference — all useful Docker and AWS CLI commands in one place.


**************************************************************************

Just built a fully automated local AWS practice environment using Terraform + LocalStack on Windows — no AWS account, no cloud costs, no credentials that expire.

The setup is a single PowerShell script that installs Docker Desktop, Terraform, AWS CLI, Python, and LocalStack, configures everything, and cleans up after itself. Five hands-on labs walk through the core AWS services you need for the Terraform Associate and AWS certifications:

🪣 Lab 01 — S3: bucket versioning, encryption, static website hosting
🗄️ Lab 02 — DynamoDB: NoSQL tables, item insertion, scanning
📬 Lab 03 — SQS: standard queues, FIFO queues, dead-letter queues
⚡ Lab 04 — Lambda: Python serverless functions, IAM roles, invocation
🌐 Lab 05 — VPC: subnets, internet gateway, route tables, security groups

Every lab has numbered scripts that create the Terraform files, deploy, verify, and destroy — so the focus stays on learning the infrastructure patterns, not fighting tooling.
Also includes a full uninstall script that removes every tool, Docker image, lab file, and temp cache when you are done.

Built this as a free alternative to cloud sandboxes for anyone preparing for HashiCorp Terraform Associate (004), AWS Cloud Practitioner, or AWS Solutions Architect Associate.
#Terraform #AWS #LocalStack #DevOps #InfrastructureAsCode #CloudCertification #AWS #HashiCorp
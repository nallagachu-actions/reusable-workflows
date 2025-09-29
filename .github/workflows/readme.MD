
# 🚀 GitHub Actions CI/CD Pipeline with AWS EKS & Helm

A **modern CI/CD workflow** using **GitHub Actions**, **Docker**, **Amazon ECR**, **AWS EKS**, and **Helm**.  
Automates build, push, and deployment of containerized apps with **security, scalability, and maintainability**.

---

## 🔹 Project Overview

Pipeline replicates **Jenkins node functionality** via **self-hosted EC2 runners**:

1. Build Docker image → tag with commit SHA/version.  
2. Push to **Amazon ECR**.  
3. Deploy to **AWS EKS** via **Helm charts**.  
4. Reusable workflows for auth, deployment, and shared logic.

*Optional diagram/image here.*

---

## ⚡ Key Features

- **CI:** Build → push to ECR → automated AWS auth.  
- **CD:** Helm deploy → staging/production → manual approval for production.  
- **Reusable Workflows:** Centralized logic, DRY principle.  
- **Secrets Management:** Scoped to org, environment, or job output.  
- **Idempotent Helm Deployments:** Only updates changed resources.

---

## 🔧 Common Issues & Fixes

| Issue | Solution |
| --- | --- |
| Terraform S3 bucket error | Correct bucket name |
| EC2 creation error (Subnet) | Updated to default VPC subnet |
| Runner inactive after reboot | `systemctl restart` + `enable` |
| Org secrets not accessible | Enable runner group access + save |

---

## 📌 Quick Start

1. **Provision Infrastructure**
```bash
terraform init
terraform apply -auto-approve
````

2. **Configure Runner:** Add EC2 instance to GitHub runner group.
3. **Start Runner**

```bash
sudo systemctl start <runner-name>
sudo systemctl enable <runner-name>
```

4. **Run Pipeline:** CI → Docker → ECR → CD → Helm → EKS.

---


* Always verify **runner group access** for org secrets.
* **Reusable workflows** = consistency + scalability.
* Helm + GitHub Actions + EKS = **secure, repeatable deployments**.
* Manual gates & environment scoping enhance **production safety**.

---

## 🔗 References

* [GitHub Actions Docs](https://docs.github.com/en/actions)
* [AWS EKS & Helm Docs](https://docs.aws.amazon.com/eks/latest/userguide/helm.html)



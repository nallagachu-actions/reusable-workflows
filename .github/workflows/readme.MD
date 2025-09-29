
# 🚀 GitHub Actions CI/CD Pipeline with AWS EKS & Helm

This repository demonstrates a **comprehensive enterprise-grade CI/CD pipeline** using **GitHub Actions**, **Docker**, **Amazon ECR**, **AWS EKS**, and **Helm**.  
The workflow automates the full lifecycle of containerized applications with a focus on **security, scalability, traceability, and operational excellence**.

---

## 📌 Project Purpose

In modern DevOps, pipelines must be **repeatable, auditable, and secure**.  
This project replicates **Jenkins node functionality** using **self-hosted GitHub Actions runners** hosted on EC2, enabling:

- Full control over runner environment and tooling.
- Integration with AWS infrastructure (ECR, EKS).
- Modular, reusable workflows for authentication, deployment, and shared steps.

**Pipeline Flow Overview:**

1. **CI Phase** – Build Docker image → Tag using **commit SHA** or **semantic version** → Push to **Amazon ECR**.  
2. **CD Phase** – Deploy Docker image to **AWS EKS** via **Helm charts** → Staging → Production with optional manual approvals.  
3. **Reusable Workflows** – Centralized steps for AWS authentication, Kubernetes deployment, and common pipeline logic.  
4. **Secrets Management** – Scoped at organization, environment, or job level for security and least privilege access.

*Add pipeline flow diagram or architecture image here.*

---

## ⚡ CI/CD Strategy & Architectural Insights

### Continuous Integration (CI)

The CI workflow ensures **traceable, secure, and reliable builds**:

- **Docker Build & Tagging**
  - Builds are deterministic, using **commit SHA or semantic version**.
  - Guarantees traceability from deployed image to source code.

- **AWS Authentication**
  - Implements **OIDC or reusable workflow authentication**.
  - Avoids long-lived AWS credentials for security.
  - Centralized in a reusable workflow to enforce **DRY principle**.

- **Push to Amazon ECR**
  - Only successful builds are pushed.
  - Ensures **immutable artifact storage** for production deployments.

**Key Insight:** Traceability is crucial for debugging and auditing, and secure authentication ensures compliance with least-privilege principles.

---

### Continuous Deployment (CD)

The CD workflow focuses on **controlled, repeatable, and environment-aware deployments**:

- **Environment-Based Deployment**
  - Staging and Production environments are defined in GitHub.
  - Manual approval gates for production deployment.
  - Prevents accidental production releases.

- **Helm Deployment**
  - Uses `helm upgrade --install` for **idempotent deployments**.
  - Dynamic image tags from CI ensure correct versions are deployed.
  - Simplifies rollback and update operations.

- **Modular Repository Structure**
  - Application code and Helm charts reside in separate repositories.
  - Allows independent development and deployment cycles.
  
- **Kubernetes Cluster Connectivity**
  - Runner authenticates to AWS → updates kubeconfig using `aws eks update-kubeconfig`.
  - Security groups configured to allow runner access to the cluster API.

**Key Insight:** Modular structure and environment scoping enhance **scalability, maintainability, and operational safety**.

---

### Reusable Workflows

Reusable workflows are the backbone of this pipeline:

- Centralize **AWS authentication, EKS deployment, and shared tasks**.
- Minimize duplication and enforce **standardization** across multiple projects.
- Require explicit **secret passing** for public repositories.
- Ensure **security, reliability, and maintainability** across all pipelines.

**Pro Tip:** Always check **runner group access** for org secrets when using reusable workflows.

---

## 🛠 Self-Hosted Runner Setup

1. **Provision EC2 via Terraform**
   - Typical issues:
     - **S3 Bucket Access Error**
       ```
       Error refreshing state: Unable to access object "roboshop-dev-runner" in S3 bucket
       ```
       ✅ Solution: Correct the bucket name.
     - **Invalid Subnet**
       ```
       InvalidSubnetID.NotFound: The subnet ID 'subnet-05ba31ef8fb5ba283' does not exist
       ```
       ✅ Solution: Use default VPC subnet ID.

2. **Configure Self-Hosted Runner**
   - Add EC2 instance to **GitHub organization runner group**.
   - Run `config.sh` for setup.
   - Start service:
   ```bash
   sudo systemctl start <runner-service-name>
   sudo systemctl enable <runner-service-name>
````

* Check status:

```bash
systemctl status <runner-service-name>
```

3. **Handling EC2 Reboots**

   * Runners may become **inactive** on reboot.
   * `systemctl enable` ensures **auto-start**.

4. **Secrets Management**

   * Organization secrets must be explicitly allowed for runner group.
   * Enable **“Allow secrets for all repositories”** for public repositories.
   * Prevents workflow errors like:

   ```
   Secret AWS_ACCESS_KEY_ID is required, but not provided
   ```

---

## 🔧 Common Issues & Resolutions

| Issue                  | Error Message                     | Resolution                          |
| ---------------------- | --------------------------------- | ----------------------------------- |
| Terraform S3 bucket    | Unable to access object           | Correct bucket name                 |
| EC2 creation           | InvalidSubnetID.NotFound          | Use default VPC subnet ID           |
| Runner inactive        | Service inactive/failed           | `systemctl restart` + `enable`      |
| Secrets not accessible | Secret AWS_ACCESS_KEY_ID required | Enable runner group access and save |

**Lesson Learned:** Runner group access is a **critical step** for pipelines using reusable workflows with org-level secrets.

---

## 📌 Step-by-Step Pipeline Execution

1. **Provision Infrastructure**

```bash
terraform init
terraform apply -auto-approve
```

2. **Register Self-Hosted Runner**

   * Add EC2 to GitHub runner group.

3. **Start Runner**

```bash
sudo systemctl start <runner-service-name>
sudo systemctl enable <runner-service-name>
```

4. **Trigger Pipeline**

   * CI builds Docker image → pushes to ECR → CD deploys via Helm → EKS.

5. **Monitor**

   * Use GitHub Actions UI to track workflow jobs and logs.

*Optional:* Add screenshots of workflow run or pipeline stages.

---

## 💡  Best Practices

* Always verify **runner group access** for org secrets.
* **Reusable workflows** enforce consistency and reduce duplication.
* Helm + GitHub Actions + EKS enables **repeatable, secure deployments**.
* Manual gates & environment scoping improve **production safety**.
* Terraform provisioning often requires **double-checking bucket names, subnet IDs, and regions**.
* Logging and monitoring runner status is essential for pipeline reliability.

---

## 🔗 References

* [GitHub Actions Documentation](https://docs.github.com/en/actions)
* [AWS EKS & Helm Deployment](https://docs.aws.amazon.com/eks/latest/userguide/helm.html)

*Images and diagrams can be added for:*

* Pipeline flow
* Runner setup
* Terraform architecture
* Helm deployment workflow



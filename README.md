# ☁️ AWS Platform Template Standardization

### Standardized Infrastructure as Code with AWS CloudFormation & AWS Service Catalog

<p align="center">

<img src="https://img.shields.io/badge/AWS-Cloud-orange?logo=amazonaws" alt="AWS">
<img src="https://img.shields.io/badge/Infrastructure--as--Code-CloudFormation-blue?logo=amazonaws" alt="CloudFormation">
<img src="https://img.shields.io/badge/Service%20Catalog-Self--Service-purple?logo=amazonaws" alt="Service Catalog">
<img src="https://img.shields.io/badge/IAM-Security-red?logo=amazonaws" alt="IAM">
<img src="https://img.shields.io/badge/EC2-Compute-yellow?logo=amazonec2" alt="EC2">
<img src="https://img.shields.io/badge/Region-ap--south--2-green" alt="AWS Region">

</p>

---

## 📌 Project Overview

This project demonstrates how **standardized AWS infrastructure** can be designed using **Infrastructure as Code (IaC)** and exposed as a **governed self-service product** using AWS Service Catalog.

### Two-stage implementation

**Stage 1 — AWS CloudFormation**

A reusable CloudFormation template is designed, visualized, validated, and deployed to create standardized application infrastructure.

**Stage 2 — AWS Service Catalog**

The same approved CloudFormation template is converted into a Service Catalog product and placed inside a controlled portfolio. Authorized users can provision the infrastructure through a self-service workflow without manually creating each AWS resource.

### Core Concept

> **CloudFormation defines and provisions the infrastructure, while Service Catalog provides governance, controlled access, versioning, and self-service provisioning around the approved infrastructure template.**

---

# 🎯 Project Objectives

- Infrastructure as Code using AWS CloudFormation
- Standardization of AWS infrastructure
- Reusable infrastructure templates
- Visual infrastructure modeling using CloudFormation Template Studio
- AWS Service Catalog product management
- Portfolio-based access control
- Service Catalog launch constraints
- IAM-based provisioning roles
- Self-service infrastructure provisioning
- CloudFormation and Service Catalog integration
- Troubleshooting IAM permission issues
- Version-controlled infrastructure configuration

---

# 🏗️ Architecture

```text
Platform Engineering Team
            │
            ▼
┌──────────────────────────┐
│ CloudFormation Template  │
│ standard-application     │
│ v1.yaml                  │
└────────────┬─────────────┘
             │
             ▼
┌──────────────────────────┐
│ AWS Service Catalog      │
│ Standard Application     │
│ Product v1               │
└────────────┬─────────────┘
             │
             ▼
┌──────────────────────────┐
│ Standard Application     │
│ Infrastructure Portfolio │
└────────────┬─────────────┘
             │
       Portfolio Access
             │
             ▼
┌──────────────────────────┐
│ Authorized End User      │
└────────────┬─────────────┘
             │
             ▼
┌──────────────────────────┐
│ Launch Constraint        │
└────────────┬─────────────┘
             │
             ▼
┌──────────────────────────┐
│ IAM Launch Role          │
│ ServiceCatalogStandard   │
│ AppLaunchRole            │
└────────────┬─────────────┘
             │
             ▼
┌──────────────────────────┐
│ AWS CloudFormation       │
│ Stack                    │
└────────────┬─────────────┘
             │
      ┌──────┼──────┐
      ▼      ▼      ▼
     VPC   Subnet   EC2
      │
      ├── Route Table
      ├── Internet Gateway
      └── Security Group
```

---

# 🔄 Deployment Workflow

```text
Define Infrastructure
        ↓
CloudFormation Template
        ↓
Template Studio Validation
        ↓
CloudFormation Stack
        ↓
CREATE_COMPLETE
        ↓
Service Catalog Product
        ↓
Portfolio + Access
        ↓
Launch Constraint
        ↓
IAM Launch Role
        ↓
Authorized User Launch
        ↓
CloudFormation Provisioning
        ↓
AVAILABLE ✅
```

---

# 🧩 Technology Stack

| Technology | Purpose |
|---|---|
| **AWS CloudFormation** | Infrastructure as Code and resource provisioning |
| **CloudFormation Template Studio** | Visual design and validation |
| **AWS Service Catalog** | Governed self-service provisioning |
| **AWS IAM** | Identity and access control |
| **Amazon EC2** | Compute instance |
| **Amazon VPC** | Network isolation and connectivity |
| **AWS Systems Manager Parameter Store** | Dynamic Amazon Linux AMI lookup |
| **Amazon S3** | Service Catalog provisioning artifact storage |
| **YAML** | Infrastructure definition |
| **JSON** | IAM policy definition |
| **Git / GitHub** | Version control and collaboration |

---

# 🚀 Stage 1 — AWS CloudFormation

## 1. Define the Infrastructure

Reusable template:

```text
cloudformation/standard-application-v1.yaml
```

Resources:

- VPC
- Internet Gateway
- Public Subnet
- Route Table
- Public Route
- Route Table Association
- Security Group
- EC2 Instance

Parameters:

```text
ApplicationName
Environment
InstanceType
```

Example:

```text
ApplicationName = standard-app
Environment     = Dev
InstanceType    = t3.micro
```

## 2. Visualize the Architecture

AWS CloudFormation Template Studio was used to visualize resource relationships before deployment.

## 3. Deploy the Stack

Stack:

```text
standard-application-dev-v1
```

Region:

```text
Asia Pacific (Hyderabad)
ap-south-2
```

Result:

```text
CREATE_COMPLETE ✅
```

The standardized infrastructure was successfully deployed.

---

# 🛒 Stage 2 — AWS Service Catalog

The validated CloudFormation template was reused as the foundation for the Service Catalog product.

## 1. Create Portfolio

```text
Standard Application Infrastructure
```

Purpose: provide a controlled collection of approved infrastructure products.

## 2. Create Product

```text
Standard Application
```

Type:

```text
CLOUD_FORMATION_TEMPLATE
```

Version:

```text
v1
```

Template:

```text
cloudformation/standard-application-v1.yaml
```

## 3. Configure Portfolio Access

The product was associated with the portfolio and access was granted to an authorized IAM user.

## 4. Configure Launch Constraint

A **LAUNCH constraint** was created for the product.

Provisioning role:

```text
ServiceCatalogStandardAppLaunchRole
```

## 5. Configure IAM Launch Role

The role is trusted by:

```text
servicecatalog.amazonaws.com
```

It provides the permissions required to provision the resources defined by the CloudFormation template.

---

# 🔐 IAM Provisioning Model

```text
End User
    ↓
AWS Service Catalog
    ↓
ServiceCatalogStandardAppLaunchRole
    ↓
AWS CloudFormation
    ↓
AWS Resources
```

The implementation required permissions involving:

- AWS CloudFormation
- Amazon EC2
- Amazon S3
- AWS Systems Manager Parameter Store

### SSM Parameter Store

The template uses:

```yaml
ImageId: '{{resolve:ssm:/aws/service/ami-amazon-linux-latest/al2023-ami-kernel-default-x86_64}}'
```

Therefore the provisioning role requires:

```text
ssm:GetParameters
```

---

# 👤 End-User Provisioning

An authorized user could access the Service Catalog product:

```text
Standard Application
```

Example launch parameters:

```text
ApplicationName = standard-app-sc
Environment     = Dev
InstanceType    = t3.micro
```

Provisioned product:

```text
standard-app-sc-v2
```

---

# ✅ Deployment Result

The Service Catalog provisioning operation completed successfully.

### Provisioned Product

```text
standard-app-sc-v2
```

### Status

```text
AVAILABLE
```

### Operation

```text
PROVISION_PRODUCT
```

### Result

```text
Succeeded
```

Service Catalog successfully created the underlying CloudFormation stack and infrastructure.

---

# 🧱 Infrastructure Created

```text
VPC
│
├── Public Subnet
├── Route Table
├── Internet Gateway
├── Security Group
└── EC2 Instance
```

CloudFormation outputs included:

- VPC ID
- Public Subnet ID
- Security Group ID
- EC2 Instance ID
- Application URL
- CloudFormation Stack ARN

---

# 🐛 Troubleshooting

## SSM `GetParameters` AccessDenied

The initial Service Catalog launch failed because the provisioning role could not retrieve the Amazon Linux AMI parameter.

### Root Cause

The provisioning chain was:

```text
End User
    ↓
Service Catalog
    ↓
ServiceCatalogStandardAppLaunchRole
    ↓
CloudFormation
    ↓
SSM Parameter Store
```

The missing permission was required by the **launch role**, not the end user.

### Resolution

Added:

```text
ssm:GetParameters
```

to:

```text
ServiceCatalogStandardAppLaunchRole
```

The product was then relaunched successfully.

### Result

```text
AVAILABLE ✅
```

### Lesson Learned

Service Catalog launch roles must contain the permissions required by every AWS service used by the underlying CloudFormation template.

---

# 📁 Repository Structure

```text
aws-platform-template-standardization/
│
├── cloudformation/
│   └── standard-application-v1.yaml
│
├── service-catalog/
│   ├── portfolio.md
│   ├── product.md
│   ├── launch-constraint.md
│   └── launch-role-policy.json
│
├── docs/
│   ├── architecture.md
│   ├── stage-1-cloudformation.md
│   ├── stage-2-service-catalog.md
│   └── troubleshooting.md
│
├── screenshots/
│
└── README.md
```

---

# 📊 CloudFormation vs Service Catalog

| Capability | CloudFormation | Service Catalog |
|---|:---:|:---:|
| Infrastructure as Code | ✅ | Uses CloudFormation |
| Resource provisioning | ✅ | Through CloudFormation |
| Reusable templates | ✅ | ✅ |
| Product catalog | ❌ | ✅ |
| Portfolio management | ❌ | ✅ |
| Controlled user access | IAM | ✅ |
| Launch constraints | ❌ | ✅ |
| Self-service provisioning | Limited | ✅ |
| Approved product versions | ❌ | ✅ |
| Governance layer | Limited | ✅ |

### Key Difference

> **CloudFormation is the infrastructure provisioning engine. Service Catalog is the governance and self-service layer built around approved CloudFormation products.**

---

# 🎓 Key Learnings

### Infrastructure as Code

Infrastructure can be defined, versioned, reviewed, and reproduced using CloudFormation templates.

### Standardization

A predefined architecture can be provided as an approved template instead of allowing every user to manually build infrastructure.

### Governance

Service Catalog controls which infrastructure products users can access and launch.

### IAM Separation

The end user does not need to directly possess all infrastructure provisioning permissions. Service Catalog can assume a dedicated provisioning role.

### Self-Service Cloud

Authorized users can provision standardized infrastructure without manually creating every AWS resource.

### IAM Troubleshooting

Deployment failures can be diagnosed by identifying the denied AWS API action and correcting the provisioning role.

---

# 🔮 Future Improvements

- GitHub Actions CI/CD
- `cfn-lint` validation
- Automated CloudFormation testing
- Pull-request based infrastructure review
- Multiple Service Catalog product versions
- Dev / Test / Production portfolios
- Least-privilege IAM policies
- AWS Organizations integration
- AWS Control Tower integration
- Automated deployment pipelines
- CloudFormation StackSets
- Additional standardized application templates
- Infrastructure monitoring and compliance checks

---

# 🏁 Conclusion

This project demonstrates an end-to-end approach for **standardized cloud infrastructure provisioning**.

The solution begins with a reusable CloudFormation template and extends it into a governed Service Catalog product.

The final architecture combines:

```text
Standardized Infrastructure
          +
Infrastructure as Code
          +
Governance
          +
Access Control
          +
Self-Service Provisioning
```

The implementation demonstrates how organizations can provide developers with **approved, reusable, and controlled infrastructure building blocks** while maintaining centralized platform governance.

---

# 👥 Project Information

**Project:** AWS Platform Template Standardization

**Primary Services:**

- AWS CloudFormation
- AWS Service Catalog
- AWS IAM
- Amazon EC2
- Amazon VPC
- AWS Systems Manager
- Amazon S3

**Region:** `ap-south-2` — Asia Pacific (Hyderabad)

---

## ⭐ Project Outcome

```text
CloudFormation Template
        ↓
Validated Infrastructure
        ↓
Service Catalog Product
        ↓
Governed Portfolio
        ↓
Authorized User
        ↓
IAM Launch Role
        ↓
CloudFormation
        ↓
AWS Infrastructure
        ↓
AVAILABLE ✅
```

**Status: Successfully Implemented**

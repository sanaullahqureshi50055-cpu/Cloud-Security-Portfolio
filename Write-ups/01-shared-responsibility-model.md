# AWS Shared Responsibility Model

## Overview

The Shared Responsibility Model splits security into two parts: what **AWS** secures, and what **the customer** secures. Neither side can fully protect a cloud environment alone — a breach happens the moment either side assumes the other has it covered.

AWS's side is called **"Security OF the Cloud."**
The customer's side is called **"Security IN the Cloud."**

## Security OF the Cloud — AWS's Responsibility

This is the physical and foundational infrastructure that every AWS service runs on top of:

- Physical data centers — building access, physical security
- Hardware — servers, hard disks, RAM, processors
- Power systems and cooling
- Network devices — physical switches, routers, core firewalls
- The hypervisor / virtualization layer
- Global infrastructure — Availability Zones, Regions, Edge Locations
- For managed services (RDS, Lambda, DynamoDB) — the underlying OS patching and service internals

AWS's job is to make sure the infrastructure itself never fails, never gets physically breached, and stays available.

## Security IN the Cloud — Customer's Responsibility

This is everything the customer configures on top of AWS's infrastructure:

- IAM — users, roles, policies, permissions, MFA
- OS-level patching **on EC2** (AWS does not patch your EC2 instance's OS — you do)
- Network configuration — Security Groups, NACLs, VPC design, subnetting, open ports
- Application-layer security — your code, your app logic
- Data classification and access control — deciding who can reach what
- Client-side encryption and secrets management
- **The decision to enable encryption** (server-side, at rest, in transit) — AWS provides the mechanism, but the customer chooses to turn it on and manages the keys

Your data and your IAM configuration are **always yours** — no matter how "managed" the service gets, this part never shifts to AWS.

## The Part Most People Miss: The Split Moves

The customer/AWS split is not fixed. It shifts depending on how abstracted the service is:

| Service Type | Example | What AWS Handles | What You Still Handle |
|---|---|---|---|
| IaaS | EC2 | Hypervisor and below | OS patching, Security Groups, IAM, everything above the hypervisor |
| Managed | RDS | Underlying OS, DB engine patching | IAM, network access, encryption toggle, Security Groups |
| Serverless | Lambda, S3, DynamoDB | OS, patching, scaling — fully managed | IAM permissions, data classification, resource policies |

The more abstracted the service, the more AWS takes over. But IAM and data are constants on the customer side at every level. This is the classic interview trap question: **"Does AWS secure your S3 bucket?"** — No. AWS secures the S3 *service*. Whether your bucket is public or private is entirely on you.

## Real-World Failure: Capital One 2019

The Capital One breach is the textbook case of a Shared Responsibility Model failure — not an AWS infrastructure breach.

An attacker exploited a **Server-Side Request Forgery (SSRF)** vulnerability in a misconfigured web application firewall running on EC2. The SSRF let the attacker query the **EC2 Instance Metadata Service (IMDS)**, which returned temporary IAM credentials tied to the EC2 instance's role. Those credentials were over-permissioned — the role had far more S3 access than the application needed. The attacker used the stolen credentials to exfiltrate data from over 100 million customer records stored in S3.

Every failure point in this chain was customer-side:
- The SSRF vulnerability — customer's application code
- IMDSv1 being enabled instead of IMDSv2 (which requires a session token, mitigating basic SSRF-to-credential-theft) — customer's EC2 configuration
- The IAM role attached to the instance had excessive permissions — customer's IAM design, violating least privilege

AWS's infrastructure did exactly what it was supposed to do. The customer's side of the model failed at three separate layers.

## Why This Justifies Offensive AWS Security as a Discipline

Cloud breaches are overwhelmingly caused by customer-side misconfiguration, not AWS infrastructure compromise: public S3 buckets, wildcard IAM policies, security groups open to `0.0.0.0/0`, IMDSv1 left enabled, missing MFA, exposed secrets in code. AWS cannot fix these for you — enforcing them is architecturally the customer's job by design.

This is exactly why offensive AWS security exists as a discipline. Every attack path in flAWS, CloudGoat, and Pacu — IAM privilege escalation, S3 bucket exfiltration, IMDS credential theft — is a simulation of a customer failing to hold up their half of this model. Learning to exploit these paths is the same skill set as learning to defend against them: understanding exactly where the line between "AWS's job" and "my job" sits, and never assuming the other side has it covered.

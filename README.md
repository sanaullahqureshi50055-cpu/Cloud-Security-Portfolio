# Cloud-Security-Portfolio
Offensive AWS security portfolio — IAM privilege escalation, S3 misconfig exploitation, and cloud attack path documentation from hands-on labs (flAWS, CloudGoat, AWSGoat, Pacu). Built while training for junior cloud security engineer roles.


## What's here
- **Write-ups**: Step-by-step exploitation of intentionally vulnerable AWS environments 
  (flAWS.cloud, CloudGoat, AWSGoat) — attack narrative, commands used, root cause, 
  and remediation for each.
- **Tooling**: Python/boto3 scripts built to automate parts of AWS misconfiguration 
  detection (public S3 buckets, overly permissive IAM policies, unencrypted EBS/RDS, 
  open security groups, missing MFA).
- **Methodology**: Every scenario is broken down using the AWS Shared Responsibility 
  Model — what AWS secures vs. what the customer configured wrong — because that's 
  the root cause of nearly every real cloud breach.

## Why this exists
Cloud environments fail because of customer-side misconfiguration, not AWS 
infrastructure breaches. This repo is a working record of finding, exploiting, and 
documenting those failure patterns — the same skill set required for cloud security 
engineering and cloud penetration testing roles.

## Status
Actively updated as part of a structured 4-month self-directed cloud security 
roadmap (June–October 2026).

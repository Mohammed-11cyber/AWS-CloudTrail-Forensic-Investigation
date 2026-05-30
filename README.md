# AWS-CloudTrail-Forensic-Investigation
Cloud incident response and log forensics parsing AWS CloudTrail JSON telemetry to map out a multi stage IAM credential compromise, privilege escalation, and S3 data exfiltration lifecycle.
# Cloud Incident Response: Parsing AWS CloudTrail for Credential Compromise

## Project Description
This incident response investigation details a forensic post-mortem analysis of a simulated enterprise AWS cloud environment breach. By triaging native AWS CloudTrail JSON log dumps within VS Code, I reconstructed a comprehensive, multi-stage attack lifecycle. The investigation successfully tracks an adversary from initial access via a compromised developer credential through administrative privilege escalation, sensitive data exfiltration, and the creation of a stealthy persistence foothold.

## Cloud Forensics Methodology and Indicators Tracked
* **Log Parsing & Noise Filtering:** Analyzed dense CloudTrail structures focusing on core keys including eventTime, eventName, sourceIPAddress, and userIdentity parameters to separate standard automated infrastructure traffic from external adversarial indicators.
* **Credential Tracking:** Isolated the active trail of compromised production Access Key ID: AKIA_DEV_PROD_9982.

## Reconstructed Cloud Attack Lifecycle

### 1. Initial Access and Reconnaissance
The threat actor initiated the breach from IP address 190.45.112.3 using a stolen production credential string. Initial log baselining caught the attacker running automated GetCallerIdentity and DescribeSnapshots API calls to map out running resources and verify current role permissions.

### 2. Administrative Privilege Escalation
To bypass role restrictions, the adversary executed a high-severity PutUserPolicy API call at timestamp 2026-04-20T02:50:33Z. This action modified active security boundaries to grant the compromised user context unrestricted administrative management rights over the entire AWS infrastructure layer.

### 3. Data Harvesting and Exfiltration
Operating under absolute administrative privileges, the attacker targeted high-value storage infrastructure. CloudTrail logs captured a GetObject call directed at S3 bucket: top-secret-financials-2026, successfully downloading a high-value asset titled ceo_personal_taxes_2025.pdf. Concurrently, Secrets Manager logs exposed the retrieval of a Base64-obfuscated string. Deobfuscation inside CyberChef revealed the direct exposure of a production database string: P@ssword_Cloud_2026!.

### 4. Persistence and Operational Evasion
To secure an unauthenticated backdoor, the threat actor triggered a CreateUser call to drop a rogue IAM identity named support_service_backup into the environment. To evade detection and blind local security teams, the attacker executed an UpdateAccessKey action to set the original compromised key state to Inactive, successfully cutting off legitimate user operations while maintaining control through their secondary backdoor identity.

## Strategic Incident Remediations
1. Immediate revocation and rotation of the compromised production access key alongside the forced deletion of the rogue backdoor IAM profile.
2. Deployment of explicit Service Control Policies (SCPs) to restrict PutUserPolicy and CreateUser actions outside of verified source IP CIDR blocks and enforce global Multi-Factor Authentication.

## Full Forensic Case File PDF
The complete cloud forensic analysis—featuring raw CloudTrail JSON snippets, VS Code environmental parsing screens, and the exact CyberChef deobfuscation recipes—is safely hosted within this repository.

Please download the complete PDF report directly from the file list view above to read the illustrated case brief!

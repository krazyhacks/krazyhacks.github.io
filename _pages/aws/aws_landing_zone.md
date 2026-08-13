---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

title: AWS Landing Zone
layout: single
permalink: /aws/aws_landing_zone/
sidebar:
   nav: "aws_nav"
---
# AWS Landing Zone
## Prerequisites
### Email naming convention
AWS accounts will be created and grouped based on organizational units. These accounts will need a root user email 
address, which cannot be changed later.  Hence, a naming convention needs to be decided and inboxes created upfront.
Naming convention: `<account_name>.<organizational_unit>.<cloud_provider>.<business_unit>@{domain_name}`
Examples: 
audit.security.aws.techgroup@acme.com

For Workload accounts within the same OU, the strategy will be to have a common inbox, but with the use of aliases to adhere to
unique email addresses per account.  This is achieved using the "+" operator in the email address.

Example for `workload_01`: `<organizational_unit>.<cloud_provider>.<business_unit>+workload_01@{domain_name}`

|     | OU                  | Sub OU       | Account        | email                                         | Notes                          |
|-----|---------------------|--------------|----------------|-----------------------------------------------|--------------------------------|
| 1.0 | ROOT                | -            | -              |                                               |                                |
| 1.0 | ROOT                | -            | management     |                                               |                                |
| 2.1 | security            | -            | audit          | audit.security.aws.bu@acme.com                |                                |
| 2.2 | security            | -            | log_archive    | log_archive.security.aws.bu@acme.com          |                                |
| 2.3 | security            | -            | security_tools | security_tools.security.aws.bu@acme.com       |                                |
| 3.0 | sandbox             | product_line | sandbox        | product_line.sandbox.aws.bu+username@acme.com | A user working on product_line |
| 4.1 | core_infrastructure | -            | network        | network.core_infra.aws.bu@acme.com            | A user working on product_line |
| 4.2 | core_infrastructure | -            | network        | network.core_infra.aws.bu@acme.com            | A user working on product_line |
| x.x | OU                  | SUB_OU       | ACC            | acc.OU.aws.bu@acme.com                        | A user working on product_line |

#### Run Book Notes
PreRquisite for New AWS Account
* AD Group (for SSO)
* Email inbox
* Terraform state bucket naming convention
* VPC CIDR Range
* AWS Organizations Design
* AWS SSO
* Control Tower
* Security
* Cost Management/Billing
* Monitoring
* Logging
* Run Book

### Terraform state bucket naming convention
[AFT Setup](https://www.youtube.com/watch?v=Y5zKAjKibEU&t=574s) Discusses some of the pre-setup
[AFT Demo](https://www.youtube.com/watch?v=8Ot5wn7kxI0_)
[AFT Terraform](https://www.youtube.com/watch?v=eDbNvHz02dk)
[AFT Customisation](https://www.youtube.com/watch?v=fDtxiBW_J8I_) - more general info using cloudformation templates, includes terraform 
    * Service Quota's - global customisation?
    * Identity: Identity Provider, IAM Roles * Policy, Service Control Policy - cannot make same IAM role/S3 in multi regions. encode region.
    * Security & Compliance: Security Tooling, Encryption, 
    * Networking: AWS Transit Gateway, IP Allocation, Routing, Security Groups
    * Logging: Cloudtrail (data events), VPC Flow logs, Firewall logs, Cloudwatch logs
    * Control: AWS Config rules, Resource policy (S3, SNS, KMS), Preconfigured products. Use `AWS Service Catalog` to automatically apply.
Separate state buckets for bootstrap - which is best placed in the management account.
This is used for configuring initial setup of security tools like AWS Config, AWS GuardDuty, AWS Security Hub, Control Tower etc.
Second state bucket in the AFT account to cover orchestration of AFT provoisioned accounts/resources.

Access: Controlled by AD group
State Bucket Name : `terraform-state-<management>-<ou>-<account_name>-<region>-<uuid>`
Folder Structure : `<ou_path>/<account_name>/<vcs_repo_name>/state-files`
Example: `security/audit/terraform-state-management-security-audit-us-east-1-1234567890.tfstate`

AFT Provisioning should NOT be done via Scalr, github actions or any other CI/CD tool.
If the third party tool is compromised, AWS account can be easily compromised, inject malicious code, open backdoors,
extract data, crypto-mining ec2 instances, Identity hijacking, ransom demands.

Initial setup of AWS should be done via Terraform, checked into VCS, executed locally using SSO session tokens,
with state files stored in S3.  Access to VCS should be restricted to a few people via AD groups.
Once the initial setup is complete, should the permissions policy be removed from that SSO AD group?

Implement multi-approval workflow for AFT provisioning.

The user would need to be in the correct AD group, 


### CIDR Range
Decide on the CIDR range for the VPCs. This will be used to create the VPCs and subnets when new accounts are created.

How to predict the CIDR range?


### AWS Organizations Design
AWS Organization design is needed to help steer the prerequisite naming convention.

While AWS allows you to nest Organizational Units (OUs) up to five levels deep, adding too many layers creates significant operational and security overhead.
The primary drawbacks of excessive OU layering for Service Control Policies (SCPs) include:

* Complexity in Troubleshooting: Understanding the "effective permissions" for a user becomes extremely difficult when policies are inherited across multiple layers. Since an explicit Deny in any parent OU overrides an Allow elsewhere, identifying exactly which layer is blocking an action can be time-consuming.
* Intersection of Policies: SCPs work by calculating the intersection of allowed actions across every level of the hierarchy (Root → Parent OU → Child OU → Account). More layers mean more points of failure where a configuration error can inadvertently break a critical service.
* Operational Friction: Developers may face frequent interruptions if deep, conservative SCPs prevent them from using new AWS services or features. Deep nesting often leads to "shadow privilege" or "governance gaps" that are difficult to audit.
* Policy Management Overhead: While you can attach up to 5 SCPs per entity (Root, OU, or Account), managing a deep tree increases the risk of reaching limits if policies aren't carefully consolidated.
* Visibility Gaps: AWS does not provide detailed error messages explaining which specific SCP caused a denial, making deep hierarchies "opaque" for administrators.

#### AWS Organizational Units

## Check List
[Check List](https://docs.aws.amazon.com/prescriptive-guidance/latest/security-reference-architecture/checklist.html)

## Account Creation
### Account lockdown
### Setup SNS for account creation notifications

## AWS SSO


### Control Tower 

## Security

[Security Reference Architecture](https://docs.aws.amazon.com/prescriptive-guidance/latest/security-reference-architecture/architecture.html)
[Service Control Policy](https://www.youtube.com/watch?v=PO_mfSonZx0)
]
* Audit Account responsible for reporting and evidence collection
* Log Archive Account responsible for log storage and retention. The central repository for all security and operational logs. It is designed for high-integrity, long-term storage and is rarely accessed by users. The Log Archive is a dedicated account for securely storing logs for archiving and forensic activities. AMS access to this account is limited to a few users; restricted to auditors and security teams for compliance and forensic investigations related to account activity.
* Security Account responsible for security services like Security Hub and GuardDuty. The "Command Center" for security operations. It hosts the delegated administration of detective and proactive security services.

|     | OU         | SubOU  | Account  | Personas                  | Policy                                                 | AD Group  | Usage Notes                                                                                                                                                                                                                   |
|-----|------------|--------|----------|---------------------------|--------------------------------------------------------|-----------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 2.1 | security   | -      | audit    | Internal Security Admin   | AWSSecurityHubFullAccess                               | -         | Responsible for the overall security posture. They use the Audit account to manage security services. Configures security services like Security Hub and GuardDuty across the organization.                                   |
| 2.1 | security   | -      | audit    | Internal Compliance team  | SecurityAudit, ReadOnlyAccess                          | -         | These users monitor the organization's adherence to regulatory standards (like PCI DSS or HIPAA). They use the account to review compliance reports and evidence collected by AWS Audit Manager. Performs cross-account read-only reviews of resource configurations for risk assessment                             |
| 2.1 | security   | -      | audit    | Internal Auditors         | SecurityAudit, ReadOnlyAccess                          | -         | Internal teams that perform independent assessments of risk. They require read-only access to all accounts in the landing zone to review configurations and security logs without the ability to modify production resources. |
| 2.1 | security   | -      | audit    | External Auditors         | CUSTOM                                                 | -         | Authorized outside entities that need to verify compliance for certifications. They are often granted time-bound, read-only access to specific audit reports and evidence                                                     |
| 2.1 | security   | -      | audit    | Audit Owner/Administrator | AWSAuditManagerAdministratorAccess                     | -         | The primary administrative persona for AWS Audit Manager. They have full permissions to create and manage assessments. Full management of audits, including creating assessments and choosing frameworks                      |
| 2.1 | security   | -      | audit    | Security Analyst/Auditors | AWSSecurityHubReadOnlyAccess, SecurityAudit / ReadOnly | -         | Delegated User. Subject matter experts (e.g., a database admin or developer) assigned to specific controls. They have limited access to upload evidence or comment on specific audit findings.                                |
| 2.1 | security   | -      | audit    | Organization Auditor      | AWSOrganizationsReadOnlyAccess                         | -         | Needs to view the organizational structure and account list to ensure audit scope                                                                                                                                                                                                                              |
| 2.1 | security   | -      | audit    | -                         | -                                                      | -         |                                                                                                                                                                                                                               |

[Services in Security Accounts](https://docs.aws.amazon.com/prescriptive-guidance/latest/security-reference-architecture/security-tooling.html)

| Audit        | Log Archive                 | Security Tooling  |
|--------------|-----------------------------|-------------------|
| Security Hub | Agregated Cloud Trail       | AWS Artifact      |
| GuardDuty    | Agregated Config Logs       | AWS Audit Manager |
|              | CloudTrail                  |                   |
|              | Config                      |                   |
|              | CloudWatch Logs             |                   |
|              | S3 Buckets                  |                   |
|              | VPC Flow Logs               |                   |
|              | CloudWatch Metrics + Alarms |                   |
|              |                             | Config Rules      |
|              |                             | Custom Actions    |

[SRA Terraform](https://github.com/aws-samples/aws-security-reference-architecture-examples/tree/main/aws_sra_examples/terraform)

## Cost Management
[Found this half way through](https://aws.amazon.com/blogs/aws-cloud-financial-management/how-dedicated-account-delegation-helped-crowdstrike-manage-costs/)
Use AFT to deploy a dedicated billing account under Finance OU.
1. Log into master account as the "ROOT" user
2. Navigate to Billing & Cost Management 
3. Under "IAM user and role access to Billing information" -> enable using check box "IAM user/role access to billing information"
4. Under "Cost Optimization Hub" -> Enable (Needs privileged IAM role.)
(FYI: Logged into Billing account as ROOT, step 3 was also enabled in billing account, even though it was done on the mgmt account.)

Delegated billing account
1. Navigate to Billing & Cost Management -> "Cost Management Preferences"
2. Under "General" Tab -> "Member Account Permissions" enable "linked account access" + "linked account refunds & credits"
3. Under "Cost Optimisation Hub" enable "Enable Cost Optimisation Hub" -> "Delegated administrator" -> Select account number for Billing account.
4. Also enable "Enable Cost Optimization Hub for all member accounts"

Share Org-Wide billing data using Billing Views

1. From Management account, go to Resource Sharing Manager (RAM)
1.1 `aws ram enable-sharing-with-aws-organization`
1.2 `aws organizations enable-aws-service-access --service-principal ram.amazonaws.com`
2. Create new resource share called "ShareBillingViews" with Billing account
3. `aws organizations enable-aws-service-access --service-principal cost-optimization-hub.bcm.amazonaws.com`
4. `aws organizations enable-aws-service-access --service-principal billing-cost-management.amazonaws.com`
5. Check
`aws organizations list-aws-service-access-for-organization \
    --query 'EnabledServicePrincipals[?ServicePrincipal==`billing-cost-management.amazonaws.com`]'
`


Thanks for the detailed writeup and screenshots. It made it easier to figure out what's going on.
 
The two things that have been enabled work slightly differently than you expect.
 
What's actually enabled
 
Cost Optimization Hub delegation to the Billing account:
 This Organizations delegated-admin feature only grants org-wide cost optimisation recommendations (rightsizing, idle resources, Savings Plans opportunities). It doesn't affect the Bills page or consolidated cost data. AWS Billing and Cost Management itself is not on the list of services that support a delegated administrator.
 
"IAM user and role access to Billing information.” :
This setting only applies within the account where it was enabled. If enabled in the management account, it just lets IAM users and roles in the management account open the Billing console instead of only the root user. If enabled in the Billing account, same thing for that account. It does not cross account boundaries.
 
Combined with AWSBillingReadOnlyAccess, the net effect is: a user in the Billing account can see the Billing account's own charges, plus org-wide Cost Optimization Hub recommendations.
 
What to add
 
To get your desired behaviour requires a collection of per-service delegations plus IAM setup, not a single toggle.
 
On top of the Cost Optimization Hub delegation already done, from the management account run:
aws organizations enable-aws-service-access --service-principal compute-optimizer.amazonaws.com

TrustedAdvisor requires you to be one the higher tier support plan;
aws organizations register-delegated-administrator \
    --account-id <billing-account-id> \
    --service-principal trustedadvisor.amazonaws.com
 
aws organizations register-delegated-administrator \
    --account-id <billing-account-id> \
    --service-principal compute-optimizer.amazonaws.com
 
Then in the Billing account, attach an IAM role with:
 
- Billing (job-function policy)
- AWSSavingsPlansFullAccess
- CostOptimizationHubAdminAccess
- ComputeOptimizerReadOnlyAccess
 
The piece that actually unlocks org-wide cost data is creating a Custom Billing View in the management account scoped to the whole org and share it to the Billing account via AWS RAM. That gives org-wide Cost Explorer, Budgets, and the Billing home page in the Billing account. Bills page and invoices stay in the management account.
 
Optionally, deliver a Cost and Usage Report / Data Export from the management account to an S3 bucket owned by the Billing account. Richest dataset which works well with Athena and QuickSight.
 
This blog post covers the pattern in greater detail covering Governance and Approval Workflows I omit [here](https://aws.amazon.com/blogs/aws-cloud-financial-management/how-dedicated-account-delegation-helped-crowdstrike-manage-costs/)
Cli command to verify support plan (must be run in us-east-1);
`aws support describe-trusted-advisor-checks --language en --region us-east-1`

To allow billing account to create budgets, needs 'iam:CreateServiceLinkedRole', from Billing account, run command (one-time admin bootstrap);
`aws iam create-service-linked-role --aws-service-name budgets.amazonaws.com`
Verify using;
`aws iam get-role --role-name AWSServiceRoleForBudgets`
Had to add custom inline policy for the BillingAdmin user/role.

### Cost Allocation + tagging
[youtube: cost allocation strategies](https://www.youtube.com/watch?v=g7KiVz95wjo)
* CREATE TAGs -> Activate TAGs in management account -> CREATE COST CATEGORIES (group typos, projects to teams)
* Charge Back - Actual AWS charge to a BU/CostCenter
* Show Back   - A report, who used what, available to all stakeholders.
* Split Charge -> Shared stuff like network, datascience split proportionally to other categories.
* Define tagging strategy to help create billing reports.
* Cost categories
Finance:
* Accountability, Forecasting, Accruals, allocation, trends, budgets
* Who should be charged what (business_unit, cost_center, product, owner)
* How are Production systems costing to run vs the maintenance/development (lifecycle, environment_class)
* Total cost of App/Product X
* Forecast for March for Product Y
* Total cost for the Development or QA Teams in 2028
* Which product has the highest storage or compute cost
* What is the cloud growth trend? (month-on-month, service trends, forecast)
* What are the unavoidable/fixed costs? (savings plans, reserved instances, Support, Data Transfers, NAT Gateway, enterprise discounts)
* Why is Product cost growing faster than revenue
* How do I allocate cost by team
* How do I allocate cost by Product/Project
* How do I allocate cost by environment
* How do I allocate shared costs (support, networking, )
* Where can we Optimise?


### Other uses for Tags
General Tag Uses:
* Resource Organisation
* Automation activities - shutdown?
* Access Control based on tag values
* Cost Allocation 
    Examples:
    * Classifying a workload
    * Listing busniess criticality
    * Defining Buisness unit
    * Documenting support Team
    * Associating a cost center
    * Defininig a patch schedule --?
Cost Management Tag
    * Cost Allocation
    * Identify unused resources - but costing money
    * Budgets and Alerts (Forecast and overspend)
    Examples:
        * Cost Center or Profit Center
        * App or Workload Name
        * Business Unit
        * Product
Key Principles:
    * Standardise
    * Enforce (Automated - IAC, CICD)
    * Audited - 
    * Maintained

### Cost Categories
After making tags active, need to leave for 24 hours before they are available for use in Cost Categories.
Most likely, we will have tags that have been renamed but mean the same thing, i.e. `CostCenter` and `CostCenterCode`.  
We can use Cost Categories to group these together, so that we can report on them as a single entity.

Go to management account -> billing -> cost categories -> create cost category

TBD: Create Billing Groups in Billing Conductor.
Core
Workloads, sandbox 


1. Cost visibility baseline
2. Budgets (minimum viable finance controls)
Using AWS Budgets

Set up:
A. Org-level budget
Monthly total cost
Alerts at:
50%
80%
100%
B. Per-account budgets
Especially for:
Sandbox/dev accounts
High-risk workloads
C. Forecast alerts
Trigger when forecast exceeds budget

## IAM
Delegate IAM Identity Center to AFT account.
Then configure using account/global customizations.

## Monitoring

## Logging


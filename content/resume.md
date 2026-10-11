+++
date = "2026-10-11"
+++

# Wouter in 't Veld

_Platform engineer in the cloud engineering team at [MyParcel](https://www.myparcel.nl). Together we build and run the [AWS](https://aws.amazon.com/) platform our product teams deploy to: accounts, deploy pipelines, DNS, networking, and the shared infrastructure code that ties it together. A lot of the job is working with those teams and making things self-service, so they can ship without waiting on us._

```
email:   hashbang@wouter.sh
github:  github.com/wouter-intveld
twitter: @wouterintveld
```

## Experience

**Senior Platform Engineer @ [MyParcel](https://www.myparcel.nl)** `June 2023 - Present`  
_We write our infrastructure in TypeScript with AWS CDK, ship it through GitHub Actions, and review each other's changes along the way._

What I worked on:

- Designed and built the Envoy private gateway, which now runs in acceptance and production. Traffic goes through CloudFront and WAF to Envoy on ECS Fargate, then on to private API Gateways. Teams register their own routes and rate limits with a CDK construct, without touching the gateway repository. A reconciler turns those registrations into Envoy config, and Envoy picks it up without a restart.
- Moved the shared `api.myparcel.nl` DNS zones into a new dns-service, driven by a single config file, and retired the older delegation service. The production cutover ran in small staged steps, and every pull request shows the DNS diff before anything deploys.
- Did most of the work on the deployment service that onboards a repository to GitHub Actions. From a shared list of AWS accounts it creates the OIDC trust, an IAM deploy role per account, and the matching GitHub environments.
- Built break-glass access for emergencies: IAM users that must set up MFA before they can do anything, cannot create access keys, and raise an alert when someone logs in.
- Run MyParcel's AWS organization as code. Since 2024 I've been the main contributor to the management service, which holds Control Tower, the account factory, the service control policies, and the IAM Identity Center groups and permission sets that decide who can access which account. StackSets roll a baseline out to every account: CDK bootstrap, a break-glass role, alarm routing, and cross-account CloudWatch monitoring. GuardDuty, Macie, and Security Hub are administered from the audit account, and a root login raises an alert.
- Looked after the infrastructure of the Core API, MyParcel's main API. I moved API Gateway to a declarative route config, added provisioned concurrency to the Lambda authorizer, turned on GuardDuty malware scanning and server access logging for S3, and upgraded the search cluster from Elasticsearch to OpenSearch, with scheduled snapshots.
- Removed AWS App Mesh from the shared CDK libraries, the VPC base stack, the Core API, and the Order API.
- Reworked alerting: alarms now page through PagerDuty, deploys show up as markers in New Relic, and I tuned the CloudWatch alarms that fired too often.

**Senior Cloud Engineer @ [KNMI](https://www.knmi.nl)** `Aug. 2020 - May 2023`  
_Led the cloud engineering team. We set the standards for deploying to AWS and designed the CI/CD setup that teams used to ship their applications._

What I worked on:

- Coached the team on AWS and led the build of the Cloud Landing Zone, with all infrastructure defined in code and deployed through automated pipelines.
- Helped teams move their applications to AWS, fixed the problems that came up along the way, and automated their infrastructure and application deploys.
- Worked with stakeholders to choose and roll out new tools, monitoring among them, which made our deploys faster and more reliable.

**Senior Cloud Engineer @ [Coolblue](https://www.coolblue.nl)** `Oct. 2015 - Jul. 2020`  
_Built a platform on AWS that let development teams deploy and run their own applications._

What I worked on:

- Designed and maintained the platform's base infrastructure and deploy pipelines.
- Helped developers move their applications onto the platform, improved their deploys, and set up monitoring so they could see how their applications performed.
- Worked between development, operations, and business teams, and evaluated new technologies for the platform.

**Senior System Engineer @ [het Oogziekenhuis Rotterdam](https://www.oogziekenhuis.nl)** `Oct. 2008 – Sep. 2015`  
_Ran the technical side of ICT projects, did system administration, and supported medical staff with technical problems. I was also the technical contact for outside suppliers._

### Earlier roles

- **All-round Engineer @ UNC Detachering** `Mar. 2007 – Dec. 2007`
- **Implementation Coordinator @ KPN Internet Solutions** `Dec. 2004 – Mar. 2007`
- **Migration Coordinator @ KPN Internet Solutions** `May 2004 – Nov. 2004`
- **Application Manager @ de Gemeente Dordrecht** `Dec. 2000 – Mar. 2004`
- **All-round Engineer @ Futureline Communications** `Jan. 1999 – Jan. 2000`

## Skills

- **AWS:** CDK, CloudFormation and StackSets, ECS and Fargate, ECR, Lambda, API Gateway, CloudFront, WAF, Route 53, VPC and VPC endpoints, NLB and ALB, Cloud Map, S3, DynamoDB, RDS, ElastiCache, OpenSearch, Systems Manager, Secrets Manager, CloudWatch
- **AWS organization:** Control Tower and Account Factory, Organizations and service control policies, IAM Identity Center
- **Networking:** Envoy (routing, rate limiting, config reloads without restarts), DNS zone delegation and migration
- **Infrastructure as code:** AWS CDK with integration tests and cdk-nag checks, Projen, Ansible, Packer
- **Languages:** TypeScript on Node.js (AWS SDK v3, Lambda Powertools, Middy, Jest), Bash, PHP, Python
- **Containers:** Docker (arm64 images, digest-pinned base images), ECS, Fargate
- **CI/CD:** GitHub Actions (OIDC to AWS, environments, automatic promotion to production), GitLab CI, publishing internal npm libraries
- **Monitoring:** CloudWatch, New Relic, PagerDuty, Datadog
- **Security:** IAM, GuardDuty, Macie, Security Hub, WAF, ACM, HashiCorp Vault
- **Ways of working:** architecture decision records, runbooks, load testing, Git, Linux

## Extra

- Fluent in English and Dutch.

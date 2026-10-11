+++
date = "2026-10-11"
+++

# Wouter in 't Veld

_I'm a platform engineer in the cloud engineering team at [MyParcel](https://www.myparcel.nl). Together we build and run the [AWS](https://aws.amazon.com/) platform that our product teams deploy to. That platform covers the AWS accounts, the deploy pipelines, DNS, networking, and the shared infrastructure code. We work closely with the product teams and build self-service tools, so they can ship without waiting on us._

```
email:   hashbang@wouter.sh
github:  github.com/wouter-intveld
twitter: @wouterintveld
```

## Experience

**Senior Platform Engineer @ [MyParcel](https://www.myparcel.nl)** `Jun. 2023 – Present`  
_We write our infrastructure in TypeScript with AWS CDK, ship it through GitHub Actions, and review each other's changes. I designed several of the platform's core services, and the team runs them together._

What I designed and built:

- The Envoy private gateway, which runs in acceptance and production. I designed the gateway, wrote the decision records for it, and built it. Traffic goes through CloudFront and WAF to Envoy on ECS Fargate, and then to private API Gateways. Teams register their own routes and rate limits with a CDK construct, without a change to the gateway repository. A reconciler turns those registrations into Envoy config, and Envoy loads the new config without a restart.
- The DNS service, which owns the shared `api.myparcel.nl` DNS zones from one config file. Every pull request shows the DNS diff before anything deploys. I also planned and ran the staged production cutover that retired the older delegation service.
- Break-glass access for emergencies. The break-glass IAM users must set up MFA before they can do anything else. They cannot create access keys, and every login triggers an alert.

What I worked on with the team:

- The AWS organization, which we run as code in the management service. I have been the main contributor to the management service since 2024. The service holds Control Tower, the account factory, and the service control policies. It also holds the IAM Identity Center groups and permission sets that decide who can access which account. StackSets roll a baseline out to every account: CDK bootstrap, a break-glass role, alarm routing, and cross-account CloudWatch monitoring. We administer GuardDuty, Macie, and Security Hub from the audit account, and a root login raises an alert.
- The deployment service, which onboards a repository to GitHub Actions. I did most of the work on it. From a shared list of AWS accounts, the service creates the OIDC trust, an IAM deploy role per account, and the matching GitHub environments.
- The infrastructure of the Core API, MyParcel's main API. I moved API Gateway to a declarative route config and added provisioned concurrency to the Lambda authorizer. For S3, I turned on GuardDuty malware scanning and server access logging. I also upgraded the search cluster from Elasticsearch to OpenSearch, with scheduled snapshots.
- The removal of AWS App Mesh from the shared CDK libraries, the VPC base stack, the Core API, and the Order API. I made those changes.
- The alerting setup. Alarms now page through PagerDuty, and deploys show up as markers in New Relic. I tuned the CloudWatch alarms that fired too often.

**Senior Cloud Engineer @ [KNMI](https://www.knmi.nl)** `Aug. 2020 – May 2023`  
_I led the cloud engineering team. We set the standards for AWS deploys and designed the CI/CD setup that teams used to ship their applications._

What I worked on:

- Coached the team on AWS.
- Led the build of the Cloud Landing Zone. We defined all of its infrastructure in code and deployed it through automated pipelines.
- Helped teams move their applications to AWS, fixed the problems they hit during the move, and automated their infrastructure and application deploys.
- Chose and rolled out new tools together with stakeholders, our monitoring among them. Those tools made our deploys faster and more reliable.

**Senior Cloud Engineer @ [Coolblue](https://www.coolblue.nl)** `Oct. 2015 – Jul. 2020`  
_I built a platform on AWS that let development teams deploy and run their own applications._

What I worked on:

- Designed and maintained the platform's base infrastructure and deploy pipelines.
- Helped developers move their applications onto the platform, improved their deploys, and set up monitoring so they could see how their applications performed.
- Acted as the link between the development, operations, and business teams.
- Evaluated new technologies for the platform.

**Senior System Engineer @ [het Oogziekenhuis Rotterdam](https://www.oogziekenhuis.nl)** `Oct. 2008 – Sep. 2015`  
_I ran the technical side of ICT projects, managed systems, and helped medical staff with IT problems. I was also the technical contact for outside suppliers._

### Earlier roles

- **All-round Engineer @ UNC Detachering** `Mar. 2007 – Dec. 2007`
- **Implementation Coordinator @ KPN Internet Solutions** `Dec. 2004 – Mar. 2007`
- **Migration Coordinator @ KPN Internet Solutions** `May 2004 – Nov. 2004`
- **Application Manager @ de Gemeente Dordrecht** `Dec. 2000 – Mar. 2004`
- **All-round Engineer @ Futureline Communications** `Jan. 1999 – Jan. 2000`

## Skills

- **AWS:** CloudFormation and StackSets, ECS on Fargate, ECR, Lambda, API Gateway, CloudFront, Route 53, VPC and VPC endpoints, NLB and ALB, Cloud Map, S3, DynamoDB, RDS, ElastiCache, OpenSearch, Systems Manager, Secrets Manager
- **AWS organization:** Control Tower and Account Factory, Organizations and service control policies, IAM Identity Center
- **Networking:** Envoy (routing, rate limits, config reloads without restarts), DNS zone delegation and migration
- **Infrastructure as code:** AWS CDK with integration tests and cdk-nag checks, Projen, Ansible, Packer
- **Programming languages:** TypeScript on Node.js (AWS SDK v3, Lambda Powertools, Middy, Jest), Bash, PHP, Python
- **Containers:** Docker (arm64 images, digest-pinned base images)
- **CI/CD:** GitHub Actions (OIDC to AWS, environments, automatic promotion to production), GitLab CI, publishing internal npm libraries
- **Monitoring:** CloudWatch, New Relic, PagerDuty, Datadog
- **Security:** IAM, GuardDuty, Macie, Security Hub, WAF, ACM, HashiCorp Vault
- **Ways of working:** architecture decision records, runbooks, load testing
- **Tools:** Git, Linux

## Languages

- Fluent in English and Dutch.

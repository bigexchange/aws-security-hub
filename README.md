# BigID - AWS Security Hub Integration
Readme 2019-10-06

## General 
BigID product helps companies manage and protect sensitive data (PII) across all their systems. It scans data sources (databases, file shares, cloud services etc) and discovers PI and data relevant to privacy regulations (GDPR, Health data, etc). E.g. if SSN is found in some table, it is most likely relevant to CCPA privacy regulation and thus needs to be reported.
Use this integration to leverage BigID's OOTB policies and receive instant findings on PII found and policy violations seamlessly in your security hub console. You can review the findings, investigate further by connecting to the BigID tool, choose the proper course of action, and create additional workflows based on the finding.

## Getting Started
These instructions will help you deploy the integration as an additional microservice component that connects to the BigID installation, pulls the relevant policies, and creates Security Hub findings (aka ‘side-car’ pattern)

## Prerequisites
BigID installation up and running
BigID API crendentials
AWS Security Hub configured
EC2 instance or VM with docker and docker compose
Permissions to publish findings on Security Hub. Either with IAM role or with AWS account id and secret
An ec2 instance provisioned with relevant IAM role to access 
E.g.

```bash
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "securityhub:*",
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": "iam:CreateServiceLinkedRole",
            "Resource": "*",
            "Condition": {
                "StringLike": {
                    "iam:AWSServiceName": "securityhub.amazonaws.com"
                }
            }
        }
    ]
}
```
Alternatively, AWS account ID and Secret

## Installing

Obtain the BigID integration docker container
Download from docker hub docker pull bigidX/aws-sec-hub-integration
Or, contact BigID Support


## Deployment
Add additional notes about how to deploy this on a live system
## Contributing
Please read CONTRIBUTING.md for details on our code of conduct, and the process for submitting pull requests to us.
## Versioning
We use SemVer for versioning. For the versions available, see the tags on this repository.
## Authors
Billie Thompson - Initial work - PurpleBooth
See also the list of contributors who participated in this project.
## License
This project is licensed under the MIT License - see the LICENSE.md file for details
## Acknowledgments
Hat tip to anyone whose code was used



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

Obtain the BigID integration docker container.
Either from docker hub: docker pull bigexchange/bigid-sec-hub.
Or, contact BigID Support


## Deployment
Link to Cloudformation template:
<contact BigID support>
    
To run the docker image you can use the following command template:
docker run -d -e BIGID_HOST='ip/url of bigid server, e.g. https://bigidserver.com' \
-e BIGID_USERNAME='user' \
-e BIGID_PASSWORD='changeme' \
-e AWS_ACCOUNT_ID='123455557890' \
-e AWS_REGION='eu-west-1' \
--name bigid-sec-hub bigexchange/bigid-sec-hub

Mandatory params:
BIGID_HOST - host of BigID product
BIGID_USERNAME - username for API access
BIGID_PASSWORD - user login for API access
AWS_ACCOUNT_ID - AWS account Id where you want to import findings
AWS_REGION - AWS region where SecurityHub is enabled

Optional params:
AWS_ACCESS_KEY_ID - AWS credentials of user who have full access to security hub
AWS_SECRET_ACCESS_KEY - AWS credentials of user who have full access to security hub
SYNC_INTERVAL_SECONDS - sync interval in seconds. Default is 5 minutes.

If you are using an image on EC2 then don't use AWS_ACCESS_KEY_ID and AWS_SECRET_ACCESS_KEY. Better to use EC2 role instead. 

## Implementation

The code was designed to be fault tolerance and cost effective.
- initially we import only violated policies;
- if policy already exists in SecurityHub we update it only when changes were detected;
- when BigID token expired, then application will relogin and take a new one without stop working;
- is case of any errors only single sync iteration will be interrupted, but code continue working;
- detailed logging provided, you can see the logs in docker container output;

## Contributing
Please read CONTRIBUTING.md for details on our code of conduct, and the process for submitting pull requests to us.
## Versioning
We use SemVer for versioning. For the versions available, see the tags on this repository.
## Authors
BigID Engineering
## License
This project is licensed under the MIT License - see the LICENSE.md file for details



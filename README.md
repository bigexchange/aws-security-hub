# BigID - AWS Security Hub Integration
Readme 2020-05-24

## General 
BigID product helps companies manage and protect sensitive data (PII) across all their systems. It scans data sources (databases, file shares, cloud services etc) and discovers PI and data relevant to privacy regulations (GDPR, Health data, etc). E.g. if SSN is found in some table, it is most likely relevant to CCPA privacy regulation and thus needs to be reported.  
Use this integration to leverage BigID's out-of-the-box policies and receive instant findings on PII found and policy violations seamlessly in your security hub console. You can review the findings, investigate further by connecting to the BigID tool, choose the proper course of action, and create additional workflows based on each finding.

## Getting Started
These instructions will help you deploy and configure the integration as an additional microservice component that connects to the BigID installation, pulls the relevant policies, and creates Security Hub findings (aka ‘side-car’ pattern).  
The integration can be used in 2 ways:  
1. As a BigID custom application (recommended) - deployed and configured as an app, params, monitoring and scheduling available for the end user (App mode, recommended)
2. As an external integration (Legacy) - running separately, configured via env variables and using an internal scheduler


## Prerequisites
* BigID installation up and running
* BigID API credentials
* AWS Security Hub configured
* EC2 instance or VM with docker and docker compose
* Permissions to publish findings on Security Hub. Either with IAM role or with AWS account id and secret
* An ec2 instance provisioned with relevant IAM role to access 
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
Either from docker hub: 
```bash
docker pull bigexchange/bigid-sec-hub
```
Or, contact BigID Support to get a download link or tar file.


## Deployment
Link to CloudFormation template: contact BigID support
    
### Run in APPLICATION mode example:
```bash
sudo docker run -p 3000:3000 -d -e BIGID_APP_MODE='APPLICATION' \
-e BIGID_APP_PORT='3000' \
--name bigid-sec-hub bigexchange/bigid-sec-hub
```
Mandatory params:  
BIGID_APP_MODE - set APPLICATION for application mode  

Optional params:  
BIGID_APP_PORT - application port, by default 3000   

#### Add the app to BigID
Follow the instructions for adding an app to BigID described in this guide:
https://www.docs.bigid.com/docs/app-framework

### Run in LEGACY mode example:
```bash
sudo docker run -d -e BIGID_APP_MODE='LEGACY' \
-e BIGID_HOST='https://bigidserver.com' \
-e BIGID_USERNAME='bigid_user' \
-e BIGID_PASSWORD='some_pw' \
-e AWS_ACCOUNT_ID='1234567890' \
-e AWS_REGION='eu-west-1' \
-e SYNC_INTERVAL_SECONDS='10' \
--name bigid-sec-hub bigexchange/bigid-sec-hub
```

Mandatory params:  
BIGID_HOST - host of BigID product
BIGID_USERNAME - username for API access  
BIGID_PASSWORD - user login for API access  
AWS_ACCOUNT_ID - AWS account Id where you want to import findings  
AWS_REGION - AWS region where SecurityHub is enabled

Optional params:  
BIGID_APP_MODE - application mode, LEGACY by default  
AWS_ACCESS_KEY_ID - AWS credentials of user who have full access to security hub  
AWS_SECRET_ACCESS_KEY - AWS credentials of user who have full access to security hub  
SYNC_INTERVAL_SECONDS - sync interval in seconds. Default is 5 minutes.  

If you are using an image on EC2, it is recommended to configure it with IAM Role instead of directly specifying AWS_ACCESS_KEY_ID and AWS_SECRET_ACCESS_KEY.


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



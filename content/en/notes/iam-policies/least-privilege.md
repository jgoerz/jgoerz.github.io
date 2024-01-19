---
title: "Least privilege"
date: 2024-01-18T10:10:00-05:00
weight: 20
---

## Pattern for least privilege

Use groups and customer managed policies to control access.

- Create a managed policy based on the AWS managed PowerUserAccess policy.
    ```json
    {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Effect": "Allow",
                "NotAction": [
                    "iam:*",
                    "organizations:*",
                    "account:*",
                    "ssm:*"  <====== added as an example
                ],
                "Resource": "*"
            },
            {
                "Effect": "Allow",
                "Action": [
                    "iam:CreateServiceLinkedRole",
                    "iam:DeleteServiceLinkedRole",
                    "iam:ListRoles",
                    "iam:ListMFADevices",          <===== added for self management
                    "iam:ListAccessKeys",          <===== added for self management
                    "iam:GetLoginProfile",         <===== added for self management
                    "iam:GetUser",                 <===== added for self management
                    "iam:ListSigningCertificates", <===== added for self management
                    "organizations:DescribeOrganization",
                    "account:ListRegions",
                    "account:GetAccountInformation"
                ],
                "Resource": "*"
            }
        ]
    }
    ```
- Create a group and attach the above policy as a permission.  Add IAM "power
  users" to group.
- Create additional groups and attach permissions you have previously denied
  (ssm example above) and allow them in the new permissions.  The example below
  would allow access to the Systems Manger in us-east-2
    ```json
    {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Effect": "Allow",
                "Action": "ssm:*",
                "Resource": [
                    "arn:aws:ssm:us-east-2:*:*"
                ]
            }
        ]
    }
    ```
- Then add the IAM users to the group
- Download [iamlive](https://github.com/iann0036/iamlive) and use it to find
  what's needed.  Additionally, use access analyzer.

### References

- [Service Authorization Reference](https://docs.aws.amazon.com/service-authorization/latest/reference/list_awsaccountmanagement.html)
- [Reference for setting up account](https://docs.aws.amazon.com/SetUp/latest/UserGuide/setup-overview.html)
- [Reference on service control policies](https://aws.amazon.com/blogs/security/how-to-use-service-control-policies-to-set-permission-guardrails-across-accounts-in-your-aws-organization/)
- [Job function policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_job-functions.html)

## SSM Parameter Store

Built-in policies

- AmazonSSMReadOnlyAccess
- AmazonSSMServiceRolePolicy
- AmazonSSMFullAccess
- [AWS docs](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-paramstore-access.html).

[Example from web](https://dev.to/hoangleitvn/07-best-practices-when-using-aws-ssm-parameter-store-6m2)
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "ssm:*"
            ],
            "Resource": "arn:aws:ssm:us-east-2::parameter/*"
        },
        {
            "Effect": "Deny",
            "Action": [
                "ssm:GetParametersByPath"
            ],
            "Condition": {
                "StringEquals": {
                    "ssm:Recursive": [
                        "true"
                    ]
                }
            },
            "Resource": "arn:aws:ssm:us-east-2:123456789012:parameter/Dev/ERP/Oracle/*"
        },
        {
            "Effect": "Deny",
            "Action": [
                "ssm:PutParameter"
            ],
            "Condition": {
                "StringEquals": {
                    "ssm:Overwrite": [
                        "false"
                    ]
                }
            },
            "Resource": "arn:aws:ssm:us-east-2:123456789012:parameter/*"
        }
    ]
}
```

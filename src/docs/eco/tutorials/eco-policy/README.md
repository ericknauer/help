# Eco AWS Policy

The Eco policy is shown below.

The policy below is relevant only for customers who have not yet signed a contract for Eco. If you have signed a contract and need the Eco policy, please contact Spot Support.

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Action": [
        "cloudformation:DescribeStacks",
        "cloudformation:GetStackPolicy",
        "cloudformation:GetTemplate",
        "cloudformation:ListStackResources",
        "dynamodb:List*",
        "dynamodb:Describe*",
        "savingsplans:List*",
        "savingsplans:Describe*",
        "ec2:Describe*",
        "ec2:List*",
        "ec2:GetHostReservationPurchasePreview",
        "ec2:GetReservedInstancesExchangeQuote",
        "elasticache:List*",
        "elasticache:Describe*",
        "es:List*",
        "es:Describe*",
        "cur:*",
        "ce:*",
        "rds:Describe*",
        "rds:ListTagsForResource",
        "redshift:Describe*",
        "trustedadvisor:*",
        "s3:List*",
        "s3:GetBucketLocation",
        "s3:ListBucketMultipartUploads",
        "s3:AbortMultipartUpload",
        "s3:ListMultipartUploadParts",
        "support:*",
        "organizations:List*",
        "organizations:Describe*"
      ],
      "Resource": ["*"],
      "Effect": "Allow",
      "Sid": "FullPolicy"
    },
    {
      "Action": [
        "s3:PutObject",
        "s3:ListBucket",
        "s3:PutObjectTagging",
        "s3:PutObjectAcl"
      ],
      "Resource": "arn:aws:s3:::sc-customer-*",
      "Effect": "Allow",
      "Sid": "S3SyncPermissions"
    },
    {
      "Action": ["s3:get*"],
      "Resource": ["arn:aws:s3:::/*", "arn:aws:s3:::<Customer Bucket>/*"],
      "Effect": "Allow",
      "Sid": "S3BillingDBR"
    }
  ]
}
```

## Policy Overview

The policy grants the following permissions for operating the Eco system.

- Access to retrieve billing reports using APIs and S3 for the Detailed Billing Report, the Cost Explorer, and the Cost & Usage Report.
- Read-only permissions (on the management account only) for the Amazon services that offer reserved capacity, such as EC2, RDS, RedShift, Elasticache, ElasticSearch, and DynamoDB.
- Access to Support & Trusted Advisor for monitoring and changes to the reserved instance service limit.

## Explanation Of Permissions In Policy

### S3 Billing Bucket

The following are permissions for the CloudFormation script to read the S3 billing bucket names.

```
"cloudformation:DescribeStacks"
"cloudformation:GetStackPolicy"
"cloudformation:GetTemplate"
"cloudformation:ListStackResources"
```

### Reserved Capacity Reservations

The following are read-only permissions for the reserved capacity reservations.

```
"dynamodb:List*"
"dynamodb:Describe*"
"ec2:Describe*"
"ec2:List*"
"ec2:GetHostReservationPurchasePreview"
"ec2:GetReservedInstancesExchangeQuote"
"elasticache:List*"
"elasticache:Describe*"
"es:List*"
"es:Describe*"
"rds:Describe*"
"rds:ListTagsForResource"
"redshift:Describe*"
```

### Cost & Usage Report

The following provides access to the Cost and Usage Report (CUR).

`"cur:*"`

### CostExplorer Data

The following provides access to CostExplorer data.

`"ce:*"`

### Service Limit

The following provides access to Service Limit information.

`"trustedadvisor:*"`

### S3 Bucket Billing Data

```
"s3:List*"
"s3:GetBucketLocation"
"s3:ListBucketMultipartUploads"
"s3:AbortMultipartUpload"
"s3:ListMultipartUploadParts"
```

Support permissions allow Eco to create tickets if it hits any reserved instance related service limits.

`"support:*"`

### Account Organization

The following permissions are used to review account organization information if necessary.

```
"organizations:List*"
"organizations:Describe*"
```

### Copy Permissions

The following permissions are required to write information from your AWS Cost & Usage report to the Eco account. These are used to synchronize the Cost & Usage report and are required for the system to work. Do not remove these lines from the policy.

```json
{
  "Sid": "S3SyncPermissions",
  "Effect": "Allow",
  "Action": [
    "s3:PutObject",
    "s3:ListBucket",
    "s3:PutObjectTagging",
    "s3:PutObjectAcl"
  ],
  "Resource": "arn:aws:s3:::sc-customer-*"
}
```

### IAM Role

This role and the corresponding permissions are issued to the Eco production and DR accounts.

```json
{
  "CloudAnalyzerRole": {
    "Type": "AWS::IAM::Role",
    "Properties": {
      "AssumeRolePolicyDocument": {
        "Version": "2012-10-17",
        "Statement": [
          {
            "Effect": "Allow",
            "Principal": {
              "AWS": [
                "arn:aws:iam::393649089167:root",
                "arn:aws:iam::884866656237:root"
              ]
            },
            "Action": "sts:AssumeRole"
          }
        ]
      },
      "ManagedPolicyArns": [
        {
          "Ref": "CloudAnalyzerManagedPolicy"
        },
        "arn:aws:iam::aws:policy/job-function/Billing",
        "arn:aws:iam::aws:policy/AWSCloudFormationReadOnlyAccess",
        "arn:aws:iam::aws:policy/AmazonEC2ReadOnlyAccess"
      ]
    }
  }
}
```

## What's New?

Learn how to [Create an Eco Policy with CloudFormation](eco/tutorials/eco-policy/create-eco-policy-with-cloudformation).

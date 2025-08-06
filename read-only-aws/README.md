# Cross-Account IAM Role CloudFormation Template

This CloudFormation template creates an IAM role that allows read-only access from a trusted AWS account.

## Overview

The template creates:
- An IAM role named `MetacosmReadOnlyRole`
- Trust relationship with a specified AWS account
- AWS managed [`ReadOnlyAccess`](https://docs.aws.amazon.com/aws-managed-policy/latest/reference/ReadOnlyAccess.html) policy attached
- Optional External ID for additional security

## Parameters

- **TrustedAccountId** (Required): The 12-digit AWS Account ID that will be trusted to assume this role
- **ExternalId** (Optional): A unique identifier to prevent confused deputy attacks. Must match the pattern `[\w+=,.@:\/-]*`

## Deployment Instructions

### Using AWS CLI

1. **Deploy with External ID (recommended):**
```bash
aws cloudformation create-stack \
  --stack-name metacosm-readonly-role \
  --template-body file://cross-account-role.yaml \
  --parameters \
    ParameterKey=TrustedAccountId,ParameterValue={{YOUR-TRUSTED-ACCOUNT-ID}} \
    ParameterKey=ExternalId,ParameterValue={{your-unique-external-id}} \
  --capabilities CAPABILITY_NAMED_IAM
```

2. **Deploy without External ID (NOT RECOMMENDED):**
```bash
aws cloudformation create-stack \
  --stack-name metacosm-readonly-role \
  --template-body file://cross-account-role.yaml \
  --parameters ParameterKey=TrustedAccountId,ParameterValue={{YOUR-TRUSTED-ACCOUNT-ID}} \
  --capabilities CAPABILITY_NAMED_IAM
```

### Using AWS Console

1. Navigate to CloudFormation in the AWS Console
2. Click "Create Stack" → "With new resources"
3. Choose "Upload a template file" and select `cross-account-role.yaml`
4. Enter stack name (e.g., `metacosm-readonly-role`)
5. Provide the TrustedAccountId parameter
6. Optionally provide an External ID
7. Review and acknowledge IAM resource creation
8. Create stack

## Stack Management

### Update Stack
```bash
aws cloudformation update-stack \
  --stack-name metacosm-readonly-role \
  --template-body file://cross-account-role.yaml \
  --capabilities CAPABILITY_NAMED_IAM
```

### Delete Stack
```bash
aws cloudformation delete-stack --stack-name metacosm-readonly-role
```

### View Outputs
```bash
aws cloudformation describe-stacks \
  --stack-name metacosm-readonly-role \
  --query 'Stacks[0].Outputs'
```

## Outputs

- **RoleArn**: The ARN of the created role
- **RoleName**: The name of the created role

## Manual Role Creation (Without CloudFormation)

For detailed step-by-step instructions on creating the role manually through the AWS Console, see [console-deployment-guide.md](./console-deployment-guide.md).

## Security Considerations

- The role grants read-only access to your AWS account
- Using an External ID is recommended for additional security
- The External ID should be a unique, hard-to-guess value
- Share the role ARN and External ID securely
- Regularly review role usage in CloudTrail

## Contact

For questions or issues: aws-support@metacosm.co

# Creating Cross-Account Read-Only Role via AWS Console

This guide walks through creating an IAM role that allows read-only access from Metacosm's AWS account (975678945968) using the AWS Management Console.

## Step-by-Step Instructions

### Step 1: Navigate to IAM

1. Sign in to the [AWS Management Console](https://console.aws.amazon.com/)
2. Navigate to **IAM** (Identity and Access Management)
3. In the left navigation pane, click **Roles**
4. Click the **Create role** button

### Step 2: Select Trusted Entity

1. For "Trusted entity type", select **AWS account**
2. Select **Another AWS account**
3. Enter Account ID provided by Metacosm.
4. ✅ Check **Require external ID**
5. Enter a random secret External ID
   - Must match pattern: `[\w+=,.@:\/-]*`
   - Save this value - you'll need to share it with Metacosm
6. Leave "Require MFA" unchecked
7. Click **Next**

### Step 3: Add Permissions

1. In the permissions policies search box, type: `ReadOnlyAccess`
2. ✅ Check the box next to **ReadOnlyAccess** AWS managed policy
3. Click **Next**

### Step 4: Name and Review

1. **Role name**: `MetacosmReadOnlyRole`
2. **Description**: `Role that can be assumed from Metacosm AWS account`
3. **Add tags** (optional but recommended):
   - Key: `Purpose` | Value: `CrossAccountAccess`
   - Key: `ExternalOrganization` | Value: `Metacosm`
   - Key: `Contact` | Value: `aws-support@metacosm.co`

### Step 5: Review Trust Policy

Before creating, verify the trust policy looks like this:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::{{METACOSM ACCOUNT ID}}:root"
            },
            "Action": "sts:AssumeRole",
            "Condition": {
                "StringEquals": {
                    "sts:ExternalId": "{{YOUR SECRET EXTERNAL ID}}"
                }
            }
        }
    ]
}
```

### Step 6: Create Role

1. Review all settings
2. Click **Create role**

## After Creation

### Retrieve Role Information

1. After creation, click on the role name to view details
2. Copy the **Role ARN** (e.g., `arn:aws:iam::YOUR_ACCOUNT_ID:role/MetacosmReadOnlyRole`)
3. Share with Metacosm:
   - Role ARN
   - External ID

### Optional: Review Permissions

1. Click on the **Permissions** tab
2. Verify **ReadOnlyAccess** policy is attached
3. Click on the policy to review specific permissions granted

## Modifying the Role

### To Change External ID:

1. Go to IAM → Roles → MetacosmReadOnlyRole
2. Click **Trust relationships** tab
3. Click **Edit trust policy**
4. Update the `sts:ExternalId` value
5. Click **Update policy**

### To Add/Remove Permissions:

1. Go to IAM → Roles → MetacosmReadOnlyRole
2. Click **Permissions** tab
3. Click **Add permissions** or **Remove** as needed

## Deleting the Role

1. Go to IAM → Roles
2. Search for `MetacosmReadOnlyRole`
3. Select the checkbox next to the role
4. Click **Delete**
5. Confirm deletion by typing the role name

## Security Best Practices

- ✅ Always use an External ID
- ✅ Keep the External ID confidential between authorized parties
- ✅ Regularly review role usage in CloudTrail
- ✅ Consider adding additional condition keys like IP restrictions if needed
- ✅ Review permissions periodically to ensure they align with requirements

## Troubleshooting

### Common Issues:

1. **"Invalid principal in policy"**
   - Verify the account ID correctly matches the id provided by Metacosm.

2. **"External ID validation failed"**
   - Ensure External ID matches exactly (case-sensitive)
   - Verify it follows the pattern: `[\w+=,.@:\/-]*`

3. **"Access Denied" when assuming role**
   - Check that the assuming account/user has `sts:AssumeRole` permission
   - Verify External ID is provided and correct
   - Ensure the trust policy is configured correctly

## Contact

For assistance or questions: aws-support@metacosm.co

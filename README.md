# TrulyYou CloudFormation Template

This CloudFormation template automatically provisions all AWS resources needed for TrulyYou.

## What Gets Created

### Storage
- **S3 Bucket**: For storing user data, liveness images, documents, etc.
  - CORS enabled for web access
  - Versioning enabled
  - Server-side encryption (AES256)
  - Private access only (use signed URLs for downloads)

### IAM User & Permissions
- **IAM User**: Dedicated user for TrulyYou application
- **Access Keys**: Automatically generated credentials
- **Policies**:
  - Full S3 access to the created bucket
  - Amazon Rekognition (facial recognition, liveness detection)
  - AWS Amplify (hosting, CI/CD)
  - Amazon Cognito (user authentication)
  - CloudFront (CDN)
  - AWS Lambda (serverless functions)

## Deployment Options

### Option 1: Deploy via AWS Console (Recommended)

Click the "Deploy to AWS" button in the TrulyYou setup wizard.

The template is served from your dashboard backend at:
`http://your-dashboard-backend-url/cloudformation/truly-aws-setup.yaml`

Steps:
1. Click the "Deploy to AWS" button
2. Review the parameters (optional: change bucket name)
3. Check the box "I acknowledge that AWS CloudFormation might create IAM resources"
4. Click "Create stack"
5. Wait 2-3 minutes for completion
6. Go to "Outputs" tab and copy the credentials

### Option 2: Deploy via AWS CLI

Download the template:
```bash
curl -O http://your-dashboard-backend-url/cloudformation/truly-aws-setup.yaml
```

Create the stack:
```bash
aws cloudformation create-stack \
  --stack-name TrulyYou \
  --template-body file://truly-aws-setup.yaml \
  --parameters ParameterKey=BucketName,ParameterValue=your-unique-bucket-name \
  --capabilities CAPABILITY_NAMED_IAM \
  --region us-east-1
```

Get the outputs:
```bash
aws cloudformation describe-stacks \
  --stack-name TrulyYou \
  --query 'Stacks[0].Outputs' \
  --region us-east-1
```

## Important Notes

1. **Save Your Credentials**: The Secret Access Key is only shown once in the CloudFormation outputs. Make sure to copy it immediately.

2. **Bucket Name**: Must be globally unique across all AWS accounts. If the default name is taken, change it during stack creation.

3. **Region**: The template deploys to the region you're currently in. We recommend `us-east-1` for most services.

4. **Costs**: AWS charges apply for:
   - S3 storage (very minimal for small usage)
   - Rekognition API calls (pay-per-use)
   - Other service usage

5. **Security**: 
   - S3 bucket is private with all public access blocked
   - Server-side encryption enabled by default
   - Use signed URLs for temporary access to objects
   - The IAM user has full access to the created S3 bucket
   - Service permissions are scoped but generous for ease of use
   - Consider restricting permissions further in production

## Cleanup

To delete all resources:

```bash
# Empty the S3 bucket first
aws s3 rm s3://your-bucket-name --recursive

# Delete the stack
aws cloudformation delete-stack --stack-name TrulyYou
```

Or via AWS Console:
1. Go to CloudFormation
2. Select the "TrulyYou" stack
3. Click "Delete"

## Troubleshooting

### "Bucket name already exists"
The bucket name must be globally unique. Try adding a suffix like `-your-company-name` or a random string.

### "Stack creation failed"
Check the Events tab in CloudFormation console for detailed error messages.

### "Access Denied" errors in TrulyYou
Verify the Access Key ID and Secret Access Key were copied correctly from CloudFormation outputs.

## Support

For issues related to:
- CloudFormation template: Open an issue in the TrulyYou repository
- AWS services: Consult AWS documentation or support


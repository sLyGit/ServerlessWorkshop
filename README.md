# Serverless Image Processing Workshop

## Part 1: Presentation Slides (10 minutes)

**Slide 1: Introduction**
- What is Serverless?
- Benefits of Serverless Architecture
- Use Case: Image Processing Pipeline

**Slide 2: AWS Services We'll Use**
- AWS Lambda
- Amazon S3
- Amazon DynamoDB
- AWS Step Functions

**Slide 3: Architecture Overview**
```
[S3 Upload] → [Step Function] → [DynamoDB] → [Process Image] → [Email]
```

**Slide 4: S3 Basics**
- Object Storage
- Event Triggers
- Presigned URLs

**Slide 5: Lambda Functions**
- Serverless Computing
- Event-Driven
- Supported Languages

**Slide 6: DynamoDB**
- NoSQL Database
- Key-Value Store
- Document Model

**Slide 7: Step Functions**
- Workflow Orchestration
- State Machine
- Error Handling

**Slide 8: Image Processing**
- Watermarking
- Thumbnailing
- Image Manipulation

**Slide 9: Security**
- IAM Roles
- Permissions
- Best Practices

**Slide 10: Workshop Overview**
- Lab Structure
- Expected Outcomes
- Resources

## Part 2: Hands-on Lab Guide (30 minutes)

### Architecture Diagram
```
[S3 Bucket]
    ↓
[Step Function]
    ↓
[DynamoDB] → [Watermark Lambda] → [Thumbnail Lambda] → [Email Lambda]
```

### Step 1: Create Resources (5 minutes)

1. Create S3 Bucket:
```bash
aws s3 mb s3://image-processing-workshop-${RANDOM}
```

2. Create DynamoDB Table:
```bash
aws dynamodb create-table \
    --table-name ImageMetadata \
    --attribute-definitions AttributeName=imageId,AttributeType=S \
    --key-schema AttributeName=imageId,KeyType=HASH \
    --provisioned-throughput ReadCapacityUnits=5,WriteCapacityUnits=5
```

### Step 2: Create Lambda Functions (10 minutes)

1. Watermark Lambda (Python):
```python
import boto3
from PIL import Image, ImageDraw, ImageFont

def lambda_handler(event, context):
    s3 = boto3.client('s3')
    dynamodb = boto3.client('dynamodb')
    
    # Get image from S3
    image = Image.open(s3.get_object(...)['Body'])
    
    # Add watermark
    draw = ImageDraw.Draw(image)
    draw.text((10, 10), "Watermark", fill='white')
    
    # Save and upload
    # Update DynamoDB status
```

2. Thumbnail Lambda (Python):
```python
def lambda_handler(event, context):
    # Similar structure to watermark
    # Create thumbnail
    # Update DynamoDB status
```

3. Email Lambda (Python):
```python
def lambda_handler(event, context):
    # Generate presigned URL
    # Send email using AWS SES
```

### Step 3: Create Step Function (10 minutes)

```json
{
  "StartAt": "RegisterImage",
  "States": {
    "RegisterImage": {
      "Type": "Task",
      "Resource": "arn:aws:states:::dynamodb:putItem",
      "Next": "ProcessWatermark"
    },
    "ProcessWatermark": {
      "Type": "Task",
      "Resource": "arn:aws:lambda:REGION:ACCOUNT:function:watermark",
      "Next": "ProcessThumbnail"
    },
    "ProcessThumbnail": {
      "Type": "Task",
      "Resource": "arn:aws:lambda:REGION:ACCOUNT:function:thumbnail",
      "Next": "SendEmail"
    },
    "SendEmail": {
      "Type": "Task",
      "Resource": "arn:aws:lambda:REGION:ACCOUNT:function:email",
      "End": true
    }
  }
}
```

### Step 4: Testing (5 minutes)

1. Upload test image:
```bash
aws s3 cp test-image.jpg s3://your-bucket-name/
```

2. Monitor Step Function execution:
- Check AWS Console
- Verify DynamoDB entries
- Check email receipt

### Success Criteria
- Image uploaded to S3
- Watermark applied
- Thumbnail created
- Email received with presigned URL
- All statuses updated in DynamoDB

### Common Issues and Troubleshooting
- IAM permissions
- Lambda timeouts
- S3 event configuration
- Step Function error handling

This lab guide provides a practical, hands-on experience while meeting the time constraints and complexity requirements for beginners.# ServerlessWorkshop

# Deploy EC2 Instance GitHub Action

## Overview

This GitHub Action workflow is designed to automate the deployment of an EC2 instance on AWS. It triggers on a push to the main branch and provisions the necessary AWS infrastructure, retrieves the latest Ubuntu AMI ID, and launches an EC2 instance from scratch.

## Workflow File: `.github/workflows/ec2Instance.yml`

```yaml
name: Deploy EC2 Instance

on:
  push:
    branches:
      - main  # Trigger on push to main branch 
    paths:
      - '.github/workflows/ec2Instance.yml'

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
    - name: Checkout code
      uses: actions/checkout@v2

    - name: Configure AWS Credentials
      uses: aws-actions/configure-aws-credentials@v2
      with:
        aws-access-key-id: ${{ secrets.ACCESS_KEY }}
        aws-secret-access-key: ${{ secrets.SECRET_KEY }}
        aws-region: us-east-1

    - name: Provision AWS Infrastructure
      id: get-all
      env:
        AWS_ACCESS_KEY_ID: ${{ secrets.ACCESS_KEY }}
        AWS_SECRET_ACCESS_KEY: ${{ secrets.SECRET_KEY }}
        AWS_DEFAULT_REGION: 'us-east-1' # Replace with your desired AWS region
      run: |
        VPC_ID=$(aws ec2 create-vpc --cidr-block 10.0.0.0/16 --query 'Vpc.VpcId' --output text)
        echo "::set-output name=VPC_ID::$VPC_ID"
        
        SUBNET_ID=$(aws ec2 create-subnet --vpc-id $VPC_ID --cidr-block 10.0.1.0/24 --query 'Subnet.SubnetId' --output text)
        echo "::set-output name=SUBNET_ID::$SUBNET_ID"
        
        SG_ID=$(aws ec2 create-security-group --group-name my-security-group --description "My security group" --vpc-id $VPC_ID --query 'GroupId' --output text)
        echo "::set-output name=SG_ID::$SG_ID"
        
        aws ec2 authorize-security-group-ingress --group-id $SG_ID --protocol tcp --port 22 --cidr 0.0.0.0/0

    - name: Retrieve the Latest Ubuntu AMI ID
      id: get-ami
      run: |
        AMI_ID=$(aws ec2 describe-images \
          --filters "Name=name,Values=ubuntu/images/hvm-ssd/ubuntu-*-amd64*" "Name=state,Values=available" \
          --query 'Images | sort_by(@, &CreationDate) | [-1].ImageId' \
          --output text)
        echo "::set-output name=AMI_ID::$AMI_ID"

    - name: Launch EC2 Instance
      run: |
        aws ec2 run-instances --image-id ${{ steps.get-ami.outputs.AMI_ID }} --count 1 --instance-type t2.micro --key-name MyKeyPair --security-group-ids ${{ steps.get-all.outputs.SG_ID }} --subnet-id ${{ steps.get-all.outputs.SUBNET_ID }}


# Summary

## Trigger
The workflow is triggered by a push to the main branch that affects the `.github/workflows/ec2Instance.yml` file.

## Jobs
### Job: deploy
- **Runs on**: `ubuntu-latest`

### Steps:
1. **Checkout code**:
   - Uses the `actions/checkout@v2` action to checkout the repository code.

2. **Configure AWS Credentials**:
   - Uses the `aws-actions/configure-aws-credentials@v2` action to configure AWS credentials from GitHub secrets.

3. **Provision AWS Infrastructure**:
   - Creates a VPC with a CIDR block of `10.0.0.0/16`.
   - Creates a subnet within the VPC with a CIDR block of `10.0.1.0/24`.
   - Creates a security group within the VPC and allows SSH (port 22) access from anywhere.
   - Outputs the IDs of the created VPC, subnet, and security group for use in subsequent steps.

4. **Retrieve the Latest Ubuntu AMI ID**:
   - Retrieves the latest available Ubuntu AMI ID and outputs it for use in the next step.

5. **Launch EC2 Instance**:
   - Launches an EC2 instance using the retrieved AMI ID, within the created subnet and security group, with the instance type `t2.micro` and a specified key pair.

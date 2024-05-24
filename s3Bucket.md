# Summary

## Trigger
The workflow is triggered by a push to the main branch that affects the `.github/workflows/s3Bucket.yml` file.

## Jobs
### Job: build
- **Runs on**: `ubuntu-latest`

### Steps:
1. **Checkout code**:
   - Uses the `actions/checkout@v4` action to checkout the repository code.

2. **AWS CLI Version**:
   - Runs a command to check the AWS CLI version using the runner's shell.

3. **Configure AWS Credentials**:
   - Uses the `aws-actions/configure-aws-credentials@v2` action to configure AWS credentials from GitHub secrets.

4. **Create Package**:
   - Runs a command to create a ZIP package of the repository, excluding `.git` files.

5. **Send Package**:
   - Runs a command to upload the created package to an S3 bucket.

## Workflow File: `.github/workflows/s3Bucket.yml`

```yaml
name: CI/CD AWS
env:
  BUCKET_NAME: "newiftnew"
  APP_NAME: "myAPP"
  PACKAGE_NAME: "my_app_ver-${{ github.sha }}.zip"
  AWS_REGION: "us-east-1"
  
on:
  push:
    branches: [ "main" ]
    paths:
      - '.github/workflows/s3Bucket.yml'

  workflow_dispatch:

# A workflow run is made up of one or more jobs that can run sequentially or in parallel 
jobs:
  # This workflow contains a single job called "build"
  build:
    # The type of runner that the job will run on
    runs-on: ubuntu-latest

    # Steps represent a sequence of tasks that will be executed as part of the job
    steps:
      # Checks-out your repository under $GITHUB_WORKSPACE, so your job can access it
      - uses: actions/checkout@v4

      # Runs a single command using the runners shell
      - name: AWSCli version
        run: aws --version

      # Runs a set of commands using the runners shell
      - name: configure AWS cred
        uses: aws-actions/configure-aws-credentials@v2
        with:
          aws-access-key-id: ${{ secrets.ACCESS_KEY }}
          aws-secret-access-key: ${{ secrets.SECRET_KEY }}
          aws-region: us-east-1
      
      - name: Create package
        run: zip -r ${{ env.PACKAGE_NAME}} ./ -x *.git*

      - name: Send Package
        run: aws s3 cp ${{ env.PACKAGE_NAME }} s3://${{ env.BUCKET_NAME }}/

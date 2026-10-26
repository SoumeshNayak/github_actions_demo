#  HOW TO Add static page to s3
# jobs:
   Checkout: actions/checkout@v4 downloads your repository into the GitHub Actions runner. Your index.html is now available in the current directory.
    GitHub Secrets: Add AWS_ACCESS_KEY_ID and AWS_SECRET_ACCESS_KEY under your repository's Settings → Secrets and variables → Actions.
  AWS credentials: configure-aws-credentials@v4 configures the AWS CLI to use your credentials and the ap-south-1 region. Change the region if your bucket is elsewhere.
   S3 sync: aws s3 sync . s3://firsts3/ syncs the current directory. --exclude "*" excludes everything, and --include "index.html" includes only that file.
  Trigger: Pushing to the main branch triggers the workflow.

name: Deploy HTML to S3
on:
  push:
    branches:
      - main
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      # 1. Checkout repository containing index.html
      - name: Checkout code
        uses: actions/checkout@v4
      # 2. Configure AWS credentials from GitHub Secrets
      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v4
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: ap-south-1
      # 3. Sync only index.html to the S3 bucket
      - name: Deploy to S3
        run: |
          aws s3 sync . s3://firsts3/ \
            --exclude "*" \
            --include "index.html"


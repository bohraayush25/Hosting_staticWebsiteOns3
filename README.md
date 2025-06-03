# Hosting_staticWebsiteOns3

We are hosting a static website (like HTML/CSS/JS files) on Amazon S3. We'll use:

CloudFront as CDN for performance & HTTPS.
Route 53 for custom domain DNS.
ACM for free SSL certificate.
GitHub + CodePipeline for CI/CD (auto deployment).
Lambda to automate CloudFront cache invalidation after deployments.



🪣 1. Create S3 Bucket for Static Hosting
Bucket Name: devopswithayush.space

Settings:

Enable Static website hosting

Set:

Index document: index.html

Error document: error.html (optional)

Disable Block all public access
![Screenshot 2025-06-03 114555](https://github.com/user-attachments/assets/19ee4081-c68f-41e1-8ec3-7c50638ebe2c)


🌐 2. Configure CloudFront
Create a CloudFront Distribution:
Origin domain: devopswithayush.space.s3-website-<region>.amazonaws.com

Viewer Protocol Policy: Redirect HTTP to HTTPS

Custom domain (CNAME): devopswithayush.space

SSL Certificate: From ACM (next step)

Default root object: index.html

Caching: Leave default (Lambda will handle invalidation)
![Screenshot 2025-06-03 114458](https://github.com/user-attachments/assets/ace66706-5bfb-4174-b452-6c94bc96731c)



3. Request SSL Certificate (ACM)
Go to ACM → Request Certificate

Choose: devopswithayush.space

Validation method: DNS

ACM will give a CNAME record—add it in Route 53 to validate ownership

Use N. Virginia region (us-east-1) since CloudFront requires that


 4. Set Up DNS in Route 53
Go to Route 53 → Hosted Zones → Create hosted zone:

Name: devopswithayush.space

Create an A record (alias) pointing to the CloudFront distribution:

Record name: devopswithayush.space

Type: A – IPv4

Alias: Yes

Alias target: Choose your CloudFront distribution

Update NS records at your domain registrar (where you purchased devopswithayush.space) to match Route 53


🚀 5. Automate Deployment with CodePipeline & GitHub
Go to CodePipeline → Create Pipeline

Source: GitHub (connect your repo)

Repo: e.g., devopswithayush-portfolio

Build stage (optional):

Use CodeBuild if you need to run a build step (npm run build)

Deploy stage:

Provider: Amazon S3

Bucket: devopswithayush.space

Extract artifact: Yes

Permissions:

CodePipeline service role must have s3:PutObject access

![Screenshot 2025-06-03 114404](https://github.com/user-attachments/assets/4cc0ce9c-65a2-4feb-af2b-0a263caead75)


🧠 6. Automate CloudFront Cache Invalidation (Lambda)
Create a Lambda function:
Runtime: Python 3.11 or Node.js

![Screenshot 2025-06-03 114431](https://github.com/user-attachments/assets/28eb6e8b-346b-48ee-824b-b85db5e38b3c)


IAM Role: Add cloudfront:CreateInvalidation permission


Trigger Lambda:
Add Lambda as a post-deploy hook in CodePipeline using a custom action




LINK TO DEMO VIDEO   https://drive.google.com/file/d/19M87dOgUgQwl_CkMqfQ6Fdta1CsyMPRG/view?usp=drive_link


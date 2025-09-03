# Setup Guide – Hosting a Secure Static Website on AWS

This guide provides the detailed step-by-step process to deploying a **secure static website** on AWS using **S3, CloudFront, ACM, and Route 53**.

---

## Prerequisites
Before starting, make sure you have:

1. **AWS Account** – With permissions to create S3 buckets, CloudFront distributions, ACM certificates, and Route 53 records.  
2. **Registered Domain** – Either in Route 53 or with an external registrar.  
3. **Website Files** – Static HTML, CSS, and JavaScript files ready to upload.  
5. **Basic Understanding of AWS Services** – Familiarity with S3, CloudFront, ACM, and Route 53.  

---

## 1. Create an S3 Bucket
1. Go to **S3 → Create Bucket**.  
2. Name the bucket the same as your domain (e.g., `www.example.com`).  
3. Choose your preferred AWS **Region**.  
4. **Uncheck Block All Public Access** and acknowledge the warning.  
5. Enable **Static Website Hosting** under **Properties**:  
   - Index document: `index.html`  
   - Error document: `error.html` (optional)  
6. Upload your static website files.  
7. Add a **Bucket Policy** to allow public read access:

```json
{
    "Version": "2012-10-17",
    "Statement": [{
        "Effect": "Allow",
        "Principal": "*",
        "Action": "s3:GetObject",
        "Resource": "arn:aws:s3:::YOUR_BUCKET_NAME/*"
    }]
}
```
## 2. Request an SSL/TLS Certificate in ACM
1. Go to **AWS Certificate Manager → Request a certificate**.  
2. Choose **Public Certificate**.  
3. Enter your domain name (e.g., `www.example.com`).  
4. Use **DNS validation** (recommended for automatic renewal).  
5. Add the required **CNAME record** to Route 53 or your DNS provider.  
6. Wait for the certificate status to show **Issued**.  

---

## 3. Create a CloudFront Distribution
1. Go to **CloudFront → Create Distribution → Web**.  
2. Set the **Origin Domain** to your S3 bucket endpoint.  
3. **Viewer Protocol Policy:** Redirect HTTP to HTTPS.  
4. **Alternate Domain Names (CNAMEs):** Enter your domain (e.g., `www.example.com`).  
5. **SSL Certificate:** Select **Custom SSL Certificate** and choose the ACM certificate.  
6. Create the distribution and wait until the status is **Deployed**.  

---

## 4. Configure Route 53
1. Go to **Route 53 → Hosted Zones → Select your domain**.  
2. Create an **A Record**:  
   - **Type:** A – IPv4 address  
   - **Alias:** Yes  
   - **Alias Target:** Select your CloudFront distribution.  
3. Save the record and wait for DNS propagation (can take several minutes to a few hours).  

---

## 5. Test Your Website
1. Open your domain in a browser (e.g., `https://www.example.com`).  
2. Verify **HTTPS** is working and the site loads correctly.  


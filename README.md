# (Week 11 Assignment)
* Student ID: SDA2020-Manal
* GitHub Repository: manal-0
* Date: May 2025

# Introduction
In this project, I deployed a MERN stack blog application on AWS. The frontend is hosted using S3 static website hosting, while the backend is deployed on EC2 running Ubuntu 22.04. The database is hosted on MongoDB Atlas, and media uploads are handled via a separate S3 bucket with proper IAM policies.

# Tools and Services Used
- **Terraform**: For setting up and configuring the infrastructure on AWS.
- **Ansible**: For automating the backend server provisioning.
- **EC2**: For hosting the backend.
- **MongoDB Atlas**: For hosting the database.
- **S3**: For hosting the frontend and media files.
- **IAM**: For setting up access policies to S3.

# Deployment Steps

### 1. Terraform Deployment

First, Initialize Terraform:
`terraform init`

Then apply the configuration with the command:
`terraform apply`

To retrieve the EC2 IP address, use:
`terraform output`

### 2.  Ansible Configuration

* SSH into the EC2 instance and install the necessary dependencies.
* Use Ansible to automate the provisioning of the backend application.

### 3. Backend Configuration & Verification
* Set up environment variables in `~/blog-app/backend/.env`:
`PORT=5000`
`MONGO_URI=your_mongo_uri_here`

* Start the backend app with PM2:
`pm2 start index.js --name blog-backend`

* Verify the backend by running:
`curl http://localhost:5000/api`

### 4. Frontend Deployment
* Build the React app using:
`npm run build`

* Sync the build folder to the S3 bucket:

`aws s3 sync ./dist s3://mern-frontend-manal --delete`

* The live frontend is accessible at the S3 URL.

### 5. Media Hosting
* Images are stored in the mern-media-manal S3 bucket.

* Set the bucket policy for public access to image URLs.

* Verify images load correctly via their public URLs.

# Infrastructure Summary
### 1. Terraform Resources
* VPC with subnets (managed by base template)

* EC2 instance (aws_instance t3.micro) named MERN-Backend

* Security group allowing inbound connections on ports 22, 80, and 5000

* IAM user (mern-s3-user) for S3 access

* Two S3 buckets:
  *mern-frontend-manal for the React frontend build
  *mern-media-manal for media uploads (images)

### 2. Backend EC2 Configuration

* Ubuntu 22.04 AMI

* Node.js & PM2 installed via Ansible

* The backend app is pulled and launched with PM2 on port 5000

### 3. Frontend Hosting (React)

* React app build synced to mern-frontend-manal S3 bucket

* Public access enabled via S3 bucket policy

### 4. Media Hosting (Images)

* Media (e.g., default.png) stored in mern-media-manal

* Bucket policy updated to allow public access

# .env Configurations

-Backend (~/blog-app/backend/.env):
    PORT=5000
    MONGO_URI=your_mongo_uri_here

-Frontend (~/blog-app/frontend/.env):
   VITE_BASE_URL=http://<backend-ec2-ip>:5000/api
   VITE_MEDIA_BASE_URL=https://mern-media-manal.s3.eu-central-1.amazonaws.com

# Issues & Solutions
### 1. Permission Denied During mkdir / .env Save
Solution: Run sudo chown -R ubuntu:ubuntu ~/blog-app/backend to fix directory ownership.

### 2. React Build Freezing
Solution: Temporarily increased EC2 instance size from t3.micro to t3.small.

### 3. Terraform Drift Warning
Solution: Re-ran terraform apply after instance size change.

### 4. Image URLs Not Loading
Solution: Set public-read ACL and updated the S3 bucket policy for image access.

### 5. Terraform Drift Warning
Solution: Re-ran terraform apply after instance size change.

# Final Verifications
 Terraform was successfully applied and infrastructure is managed.

 Backend is running on port 5000.

 MongoDB Atlas is connected.

 React frontend is live via the S3 bucket.

 Images are being served from the S3 media bucket.
 
# Screenshots
Captured screenshots include:

Terraform plan/apply output

S3 bucket configurations

Running backend app in PM2

React frontend live on S3

Image URL working

.env content verification

# Cleanup:
After completing the assignment:

Used terraform destroy to clean up all AWS resources.

Removed any sensitive credentials from the EC2 instance.

Deleted MongoDB Atlas users and IP access rules.

Revoked or deleted IAM credentials.

# Conclusion
This assignment was a great opportunity to apply Terraform, Ansible, and AWS together in a real-world scenario. 

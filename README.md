TASK 1: Hosting an HTML Website on a Single EC2 Instance in AWS using S3

This guide outlines a step-by-step process for hosting an HTML website on a single EC2 instance in the default VPC using an automated script and user data at launch.

Step 1: Download the Web Files

Download the Web File:

Use the following command to download the web files:

bash

Copy code

wget https://aosnote-assignments.s3.amazonaws.com/assignment-1/mole.zip

Step 2: Upload the Web Files to an S3 Bucket

Create an S3 Bucket:

Go to the AWS Management Console, navigate to the S3 service, and click "Create bucket".

Name your bucket, choose the appropriate region, and configure the settings.

Generate and Apply a Bucket Policy:

Generate a bucket policy and verify that it allows access to the files.

Upload the Web Files:

Upload the  files into the S3 bucket:

Go to your newly created bucket, click "Upload", and drag the files into the area provided.

Click "Upload" to complete the process.

Enable Public Access:

Ensure that the uploaded files are publicly accessible by enabling public access for the bucket and setting the object ownership to ACL.

Make all objects or files in the S3 bucket publicly accessible using the ACL.

Step 3: Create a Script to Download and Host the Website on an EC2 Instance

Create a script that will be used by the EC2 instance to download the web files from the S3 bucket and host them using Apache.

Sample Script (host-website.sh):

bash

Copy code

#!/bin/bash

# Update the instance

yum update -y

# Install Apache Web Server

yum install -y httpd

# Start the Apache Web Server

systemctl start httpd

systemctl enable httpd

# Download and extract the web files from the S3 bucket

aws s3 cp s3://my-bucket-cloudrookie/mole.zip /var/www/html/mole.zip

unzip /var/www/html/mole.zip -d /var/www/html/

# Set correct permissions

chmod -R 755 /var/www/html/

# Restart the web server

systemctl restart httpd

Explanation of the Script:

yum update -y: Updates the packages on the instance.

yum install -y httpd: Installs the Apache web server.

systemctl start httpd and systemctl enable httpd: Starts Apache and sets it to start on system boot.

aws s3 cp s3://your-bucket-name/ /var/www/html/ --recursive: Downloads the contents from the S3 bucket.

chmod -R 755 /var/www/html: Sets the correct permissions.

systemctl restart httpd: Restarts Apache to apply all changes.

IAM Policy:

Create and attach an IAM policy (AmazonS3ReadOnlyAccess) for EC2 to access the S3 bucket.

Step 4: Launch an EC2 Instance with the Script in User Data

Launch a New EC2 Instance:

Go to the EC2 Dashboard and click "Launch Instance".

Choose the Amazon Linux 2 AMI.

Attach the IAM policy (AmazonS3ReadOnlyAccess) to allow access to the S3 bucket.

Configure Instance Details:

Ensure the instance is in the default VPC with a public subnet to provide internet access.

User Data Configuration: Paste the above script in the "User Data" section to automate the web server setup when the instance starts.

User Data Script Example:

bash

Copy code

#!/bin/bash

yum update -y

yum install -y httpd

systemctl start httpd

systemctl enable httpd

aws s3 cp s3://my-bucket-cloudrookie/mole.zip /var/www/html/mole.zip

unzip /var/www/html/mole.zip -d /var/www/html/

chmod -R 755 /var/www/html/

systemctl restart httpd

Configure Security Group:

Ensure that the security group attached to the instance allows inbound traffic on port 80 (HTTP) for web access.

Enable Auto Assign Public IP to ensure the instance has a public IP address for internet access.

Review and Launch:

Review the configuration and click "Launch".

Choose an existing key pair or create a new one to access the instance later.

Step 5: Verify the Website is Hosted

Access the Website:

After the instance launches, go to the EC2 Dashboard, locate the instance, and copy its public IP address or DNS name.

Open a browser and enter the public IP address or DNS name (e.g., http://<public-ip>).

Check the Web Page:

If the setup was successful, the HTML website should be visible, confirming the process worked as expected.

Summary

Download and Upload Files: Download the web files and upload them to an S3 bucket.

Create a Hosting Script: Write a script to install Apache, download the files from S3, and host the website.

Launch EC2 with User Data: Add the script to the EC2 instance’s User Data field to automate the setup process.

Verify Deployment: Access the website via the instance’s public IP address or DNS name to verify that it’s hosted properly.

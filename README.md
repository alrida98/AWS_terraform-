# AWS_terraform

# Project Description

In the Project we put to use the concepts of terraform along with AWS S3 bucket to host a simple web application 

# Componenets used

1. AWS S3 bucket

* The AWS S3 bucket is used to store the web configuration HTML files

2. Terraform

* Using the terraform IAC tool we write a terraform configuration file. We have reffered to the official doumentation as per : https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/s3_bucket_website_configuration

3. Basic HTML cwebsite

*We have used a basic HTML website to demonstrate the same 


# Steps

Step 1: 
Install Terraform and the AWS Command Line Interface (CLI) on your local machine. Configure your AWS credentials by running aws configure and providing your AWS access key and secret key.

Step 2: Define Your Website Content
To prepare static website files (HTML), place them in the directory where your Terraform configuration files are located. Name the main HTML file "index.html," and optionally, you can also include an "error.html" file. 


Step 3: Terraform Configuration File Syntax
If we want to Create a terraform configuration file we have to use .tf (e.g., main.tf) to define the infrastructure as code using terraform.

Step 4: Define your Configuration Files in your IDE

Define provider.tf file using the below code :

```
provider "aws" {
    region = "us-east-1"
}
```



In your Integrated Development Environment (IDE), open the terminal and navigate to the directory where you have created these configuration files.
After navigating to the directory where your configuration files are located in your IDE's terminal, you can run the following command to initialize Terraform and prepare it for use with AWS:

```
terraform init

```
Running terraform init will install the necessary plugins and modules required for connecting to AWS and managing your infrastructure.

Define main.tf where we create a AWS s3 bucket : https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/s3_bucket
```
resource "aws_s3_bucket" "bucket1" {
    bucket = "web-bucket"
  
}

And then add the below codes in main.tf file :



resource "aws_s3_bucket_public_access_block" "bucket1" {
  bucket = aws_s3_bucket.bucket1.id

  block_public_acls       = false
  block_public_policy     = false
  ignore_public_acls      = false
  restrict_public_buckets = false
}

resource "aws_s3_object" "index" {
  bucket = "aws_s3_bucket.bucket1.id"
  key    = "index.html"
  source = "index.html"
  content_type = "text/html"
}

resource "aws_s3_object" "error" {
  bucket = "aws_s3_bucket.bucket1.id"
  key    = "error.html"
  source = "error.html"
  content_type = "text/html"
}


resource "aws_s3_bucket_website_configuration" "bucket1" {
  bucket = aws_s3_bucket.bucket1.id

  index_document {
    suffix = "index.html"
  }

  error_document {
    key = "error.html"
  }

}

resource "aws_s3_bucket_policy" "public_read_access" {
  bucket = aws_s3_bucket.bucket1.id
  policy = <<EOF
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
	  "Principal": "*",
      "Action": [ "s3:GetObject" ],
      "Resource": [
        "${aws_s3_bucket.bucket1.arn}",
        "${aws_s3_bucket.bucket1.arn}/*"
      ]
    }
  ]
}
EOF
}
```



To run the teraform code : 
```
terraform apply -auto-approve
```
The code above will apply the necessary configurations for features such as static website hosting, bucket policies, and blocking public access to your bucket.
Change the bucket name, region, and configurations as per your requirements when using the code from the Terraform documentation.


Step 5: Define the Output file
We use an output file to obtain your website link in your IDE, eliminating the need to access the link through the AWS Console.

```
output "websiteendpoint" {
    value =aws_s3_bucket.bucket1.website_endpoint
  
}

```

Step 6: Verify the Output
Copy the link and paste the URL  in browser, which we got from output terminal 

Summary of the code:
 - Creates an S3 bucket
 - Configures the bucket's public access settings to allow public access
 - Uploads two files: index.html and error.html to the bucket. These will be used for the static website.
 - Configures the bucket for static website hosting, setting index.html as the default page and error.html as the error page.
 - Sets a bucket policy that allows public read access to the contents of the bucket. https://docs.aws.amazon.com/AmazonS3/latest/userguide/example-bucket-policies.html
 - The final result is a publicly accessible static website hosted on AWS S3, with index.html as the homepage and error.html as the error page.

Links : 


* https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/s3_bucket_website_configuration
* https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/s3_bucket.html
* https://docs.aws.amazon.com/AmazonS3/latest/userguide/example-bucket-policies.html


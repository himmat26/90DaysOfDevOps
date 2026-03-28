# Challenge Tasks

### Task 1: Understand Infrastructure as Code
Before touching the terminal, research and write short notes on:

1. What is Infrastructure as Code (IaC)? Why does it matter in DevOps?
2. What problems does IaC solve compared to manually creating resources in the AWS console?
3. How is Terraform different from AWS CloudFormation, Ansible, and Pulumi?
4. What does it mean that Terraform is "declarative" and "cloud-agnostic"?

Write this in your own words -- not copy-pasted definitions.

---

### Task 2: Install Terraform and Configure AWS
1. Install Terraform:
```bash
# macOS
brew tap hashicorp/tap
brew install hashicorp/tap/terraform

# Linux (amd64)
wget -O - https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
sudo apt update && sudo apt install terraform

# Windows
choco install terraform
```
O/P:
The following NEW packages will be installed:
  terraform
0 upgraded, 1 newly installed, 0 to remove and 26 not upgraded.


2. Verify:
```bash
terraform -version

O/P:
ubuntu@ip-172-31-37-115:~/terraform-basics$ terraform -version
Terraform v1.14.8
on linux_amd64

```

3. Install and configure the AWS CLI:
```bash
aws configure
# Enter your Access Key ID, Secret Access Key, default region (e.g., ap-south-1), output format (json)
```

4. Verify AWS access:
```bash
aws sts get-caller-identity

O/P:
ubuntu@ip-172-31-37-115:~/terraform-basics$ aws sts get-caller-identity
{
    "UserId": "AIDAWTHV2UZW7DSEZ5332",
    "Account": "453634598509",
    "Arn": "arn:aws:iam::453634598509:user/dhurandhar-terraform"
}

```

You should see your AWS account ID and ARN.

---

### Task 3: Your First Terraform Config -- Create an S3 Bucket
Create a project directory and write your first Terraform config:

```bash
mkdir terraform-basics && cd terraform-basics

O/P:
ubuntu@ip-172-31-37-115:~/terraform-basics$
```

Create a file called `main.tf` with:
1. A `terraform` block with `required_providers` specifying the `aws` provider
    

2. A `provider "aws"` block with your region
    provider "aws" {
    region = "eu-north-1"   
    }
3. A `resource "aws_s3_bucket"` that creates a bucket with a globally unique name
    

    resource "aws_s3_bucket"  "terraform_automated" {
    bucket = "my-tf-test-bucket"
    }


Run the Terraform lifecycle:
```bash
terraform init      # Download the AWS provider
terraform plan      # Preview what will be created
terraform apply     # Create the bucket (type 'yes' to confirm)

O/P:
init
Terraform has been successfully initialized!
plan
Terraform will perform the following actions:

  # aws_s3_bucket.terraform_automated will be created
  + resource "aws_s3_bucket" "terraform_automated" {
      + acceleration_status         = (known after apply)
      + acl                         = (known after apply)
      + arn                         = (known after apply)
      + bucket                      = "my-tf-test-bucket"
      + bucket_domain_name          = (known after apply)
      + bucket_namespace            = (known after apply)
      + bucket_prefix               = (known after apply)
      + bucket_region               = (known after apply)
      + bucket_regional_domain_name = (known after apply)
      + force_destroy               = false
      + hosted_zone_id              = (known after apply)
      + id                          = (known after apply)
      + object_lock_enabled         = (known after apply)
      + policy                      = (known after apply)
      + region                      = "eu-north-1"
      + request_payer               = (known after apply)
      + tags_all                    = (known after apply)
      + website_domain              = (known after apply)
      + website_endpoint            = (known after apply)
apply
Plan: 1 to add, 0 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

aws_s3_bucket.terraform_automated: Creating...
aws_s3_bucket.terraform_automated: Creation complete after 1s [id=hawk-terraform-fb]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.

terraorm destroy

aws_s3_bucket.terraform_automated: Destroying... [id=hawk-terraform-fb]
aws_s3_bucket.terraform_automated: Destruction complete after 0s

Destroy complete! Resources: 1 destroyed.


```

Go to the AWS S3 console and verify your bucket exists.
    hawk-terraform-fb bucket created in AWS S3 console.

**Document:** What did `terraform init` download? What does the `.terraform/` directory contain?

- Finding latest version of hashicorp/aws...
- Installing hashicorp/aws v6.38.0...
- Installed hashicorp/aws v6.38.0 (signed by HashiCorp)
The `terraform init` command initializes the Terraform working directory and downloads the necessary provider plugins specified in the configuration. In this case, it downloaded the AWS provider plugin version 6.38.0.

The `.terraform/` directory contains the downloaded provider plugins. inside terraform directory providers is there inside which aws provider is there. This directory is created when you run `terraform init` and it stores the plugins that Terraform uses to interact with the specified cloud providers (like AWS in this case). It ensures that Terraform has the necessary code to manage resources on AWS as defined in your configuration files.
---

### Task 4: Add an EC2 Instance
In the same `main.tf`, add:
1. A `resource "aws_instance"` using AMI `ami-0f5ee92e2d63afc18` (Amazon Linux 2 in ap-south-1 -- use the correct AMI for your region)
2. Set instance type to `t2.micro`
3. Add a tag: `Name = "TerraWeek-Day1"`

    resource "aws_instance" "example" {
    ami           = "ami-080254318c2d8932f"
    instance_type = "t3.micro"

    tags = {
        Name = "HelloWorld"
    }
}

Run:
```bash
terraform plan      # You should see 1 resource to add (bucket already exists)
terraform apply
```
aws_instance.example: Creating...
aws_instance.example: Still creating... [00m10s elapsed]
aws_instance.example: Creation complete after 12s [id=i-0555a0420a76e8a13]


Go to the AWS EC2 console and verify your instance is running with the correct name tag.

**Document:** How does Terraform know the S3 bucket already exists and only the EC2 instance needs to be created?

when we run 'terraform apply' it checks the current state of bucket in AWS and compares it with the desired state defined in our `main.tf` file. Since the bucket already exists, Terraform recognizes that it does not need to create it again. It only identifies that the EC2 instance is missing and needs to be created to match the desired state. This is because Terraform maintains a state file (`terraform.tfstate`) that keeps track of all the resources it manages.

    aws_s3_bucket.terraform_automated: Refreshing state... [id=hawk-terraform-fb]

---

### Task 5: Understand the State File
Terraform tracks everything it creates in a state file. Time to inspect it.

1. Open `terraform.tfstate` in your editor -- read the JSON structure
2. Run these commands and document what each returns:
```bash
terraform show                          # Human-readable view of current state
terraform state list                    # List all resources Terraform manages
terraform state show aws_s3_bucket.<name>   # Detailed view of a specific resource
terraform state show aws_instance.<name>
```

3. Answer these questions in your notes:
   - What information does the state file store about each resource?
     It stores the mapping between Terraform config and real infrastructure, including resource IDs, attributes, and dependencies.
   - Why should you never manually edit the state file?
     Manual edits can corrupt state and cause mismatch, leading to resource duplication or accidental deletion.
   - Why should the state file not be committed to Git?
     It may contain sensitive data and causes conflicts; use a remote backend instead.

---

### Task 6: Modify, Plan, and Destroy
1. Change the EC2 instance tag from `"TerraWeek-Day1"` to `"TerraWeek-Modified"` in your `main.tf`
    Changed the name tag of EC2 instance from "HelloWorld" to "TerraWeekDay01"
    "Name" = "HelloWorld" -> "TerraWeekDay01"

2. Run `terraform plan` and read the output carefully:
   - What do the `~`, `+`, and `-` symbols mean?
        `~` means update in-place, `+` means create, and `-` means destroy.
   - Is this an in-place update or a destroy-and-recreate?
      aws_instance.example will be updated in-place
      
      ~ means in-place update; -/+ (or destroy + create) means Terraform will recreate the resource instead of updating it.

3. Apply the change
    aws_instance.example: Modifying... [id=i-0555a0420a76e8a13]
    aws_instance.example: Modifications complete after 1s [id=i-0555a0420a76e8a13]

4. Verify the tag changed in the AWS console
    Verfied the tag of EC2 instance in AWS console, it is now "TerraWeekDay01"

5. Finally, destroy everything:
```bash
terraform destroy

O/P:
    aws_s3_bucket.terraform_automated: Destroying... [id=hawk-terraform-fb]
    aws_instance.example: Destroying... [id=i-0555a0420a76e8a13]
    aws_s3_bucket.terraform_automated: Destruction complete after 0s
    Destroy complete! Resources: 2 destroyed.

```
6. Verify in the AWS console -- both the S3 bucket and EC2 instance should be gone
    Verified in AWS console, both the S3 bucket and EC2 instance are destroyed and no longer exist.

---


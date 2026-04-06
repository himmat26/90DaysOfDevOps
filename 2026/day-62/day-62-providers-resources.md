## Challenge Tasks

### Task 1: Explore the AWS Provider
1. Create a new project directory: `terraform-aws-infra`
2. Write a `providers.tf` file:
   - Define the `terraform` block with `required_providers` pinning the AWS provider to version `~> 5.0`
   - Define the `provider "aws"` block with your region
      terraform {
         required_providers {
            aws = {
            source  = "hashicorp/aws"
            version = "~> 5.0"
            }
         }
      }

      provider "aws" {
          region = "eu-north-1"
      }

3. Run `terraform init` and check the output -- what version was installed?
   Initializing the backend...
   Initializing provider plugins...
   - Reusing previous version of hashicorp/aws from the dependency lock file
   - Using previously-installed hashicorp/aws v5.100.0

4. Read the provider lock file `.terraform.lock.hcl` -- what does it do?
   It is a dependency lock file created automatically when we run terraform init. It locks the exact version of providers Terraform will use. .terraform.lock.hcl ensures Terraform uses the same provider version everywhere.

**Document:** What does `~> 5.0` mean? How is it different from `>= 5.0` and `= 5.0.0`?
   ~> 5.0 - Terraform can choose any version like 5.0, 5.1, 5.2, etc. Major version must be 5, minor can be anything.
   >= 5.0 - Terraform can choose any version like 5.0, 5.1, 6.0, etc.
   = 5.0.0 - Terraform must use exactly version 5.0.
---

### Task 2: Build a VPC from Scratch
Create a `main.tf` and define these resources one by one:

1. `aws_vpc` -- CIDR block `10.0.0.0/16`, tag it `"TerraWeek-VPC"`
   resource "aws_vpc" "main" {
     cidr_block = "10.0.0.0/16"
      tags = {
       Name = "TerraWeek-VPC"
     }
   }

2. `aws_subnet` -- CIDR block `10.0.1.0/24`, reference the VPC ID from step 1, enable public IP on launch, tag it `"TerraWeek-Public-Subnet"`
   resource "aws_subnet" "main" {
      vpc_id     = aws_vpc.main.id
         cidr_block = "10.0.1.0/24"
         map_public_ip_on_launch = true
         tags = {
         Name = "TerraWeek-Subnet"
   }
   }

3. `aws_internet_gateway` -- attach it to the VPC
   resource "aws_internet_gateway" "main" {
       vpc_id = aws_vpc.main.id
   }

4. `aws_route_table` -- create it in the VPC, add a route for `0.0.0.0/0` pointing to the internet gateway
   resource "aws_route_table" "main" {
     vpc_id = aws_vpc.main.id

     route {
      cidr_block = "0.0.0.0/0"
      gateway_id = aws_internet_gateway.main.id
     }
   }

5. `aws_route_table_association` -- associate the route table with the subnet
   resource "aws_route_table_association" "main" {
      subnet_id      = aws_subnet.main.id
      route_table_id = aws_route_table.main.id
   }
   
Run `terraform plan` -- you should see 5 resources to create.

**Verify:** Apply and check the AWS VPC console. Can you see all five resources connected?
   aws_vpc.main: Creating...
   aws_vpc.main: Creation complete after 6s [id=vpc-0ea1d85b84fbc6ac8]
   aws_internet_gateway.main: Creating...
   aws_subnet.main: Creating...
   aws_internet_gateway.main: Creation complete after 2s [id=igw-0d49fe8f5ed5f20be]
   aws_route_table.main: Creating...
   aws_route_table.main: Creation complete after 3s [id=rtb-07bf4f3fb06a2fd8d]
   aws_subnet.main: Still creating... [00m10s elapsed]
   aws_subnet.main: Creation complete after 13s [id=subnet-0d82915890dc117bc]
   aws_route_table_association.main: Creating...
   aws_route_table_association.main: Creation complete after 1s [id=rtbassoc-0e062d7a08d59d863]

   Apply complete! Resources: 5 added, 0 changed, 0 destroyed.

---

### Task 3: Understand Implicit Dependencies
Look at your `main.tf` carefully:

1. The subnet references `aws_vpc.main.id` -- this is an implicit dependency
2. The internet gateway references the VPC ID -- another implicit dependency
3. The route table association references both the route table and the subnet

Answer these questions:
- How does Terraform know to create the VPC before the subnet?
   This is because of reference based dependecy, Subnet is using aws_vpc.main.id, so terraform understands subnet depends on VPC. Terraform internally creates dependency graph and hence creates VPC first then subnet, we don't need to write anything extra this is because of implicit dependency.

- What would happen if you tried to create the subnet before the VPC existed?
   It will failed with VPC not found because subnet required vpc id which doesn't exists.

- Find all implicit dependencies in your config and list them
   subnet - vpc
   internet gateway - vpc
   route table - vpc
   route - internet gateway
   route table association - vpc + subnet

---

### Task 4: Add a Security Group and EC2 Instance
Add to your config:

1. `aws_security_group` in the VPC:
   - Ingress rule: allow SSH (port 22) from `0.0.0.0/0`
   - Ingress rule: allow HTTP (port 80) from `0.0.0.0/0`
   - Egress rule: allow all outbound traffic
   - Tag: `"TerraWeek-SG"`

         resource "aws_security_group" "main" {
         name        = "TerraWeek-SG"
         description = "Allow TLS inbound traffic and all outbound traffic"
         vpc_id      = aws_vpc.main.id

         tags = {
            Name = "TerraWeek-SG"
         }
         }

         resource "aws_vpc_security_group_ingress_rule" "main" {
         security_group_id = aws_security_group.main.id
         cidr_ipv4         = "0.0.0.0/0"
         from_port         = 22
         ip_protocol       = "tcp"
         to_port           = 22
         }

         resource "aws_vpc_security_group_ingress_rule" "main1" {
         security_group_id = aws_security_group.main.id
         cidr_ipv4         = "0.0.0.0/0"
         from_port         = 80
         ip_protocol       = "tcp"
         to_port           = 80
         }

         resource "aws_vpc_security_group_egress_rule" "example" {
         security_group_id = aws_security_group.main.id
         cidr_ipv4   = "0.0.0.0/0"
         from_port   = 0
         ip_protocol = "tcp"
         to_port     = 0
         }

2. `aws_instance` in the subnet:
   - Use Amazon Linux 2 AMI for your region
   - Instance type: `t2.micro`
   - Associate the security group
   - Set `associate_public_ip_address = true`
   - Tag: `"TerraWeek-Server"`

      data "aws_ami" "main" {
      most_recent = true
      owners = ["amazon"]
      filter {
         name   = "name"
         values = ["ubuntu/images/hvm-ssd-gp3/ubuntu-noble-24.04-amd64-server-*"]
      }
      }

      resource "aws_instance" "main" {
      ami           = data.aws_ami.main.id
      instance_type = "t3.micro"
      subnet_id = aws_subnet.main.id
      vpc_security_group_ids = [aws_security_group.main.id] 
      associate_public_ip_address = true

      tags = {
         Name = "TerraWeek-Server"
      }
      }

Apply and verify -- your EC2 instance should have a public IP and be reachable.
      AWS instance is created with correct security group and inbound/outbound rules.

---

### Task 5: Explicit Dependencies with depends_on
Sometimes Terraform cannot detect a dependency automatically.

1. Add a second `aws_s3_bucket` resource for application logs
2. Add `depends_on = [aws_instance.main]` to the S3 bucket -- even though there is no direct reference, you want the bucket created only after the instance
3. Run `terraform plan` and observe the order

   resource "aws_s3_bucket" "name" {
      depends_on = [aws_instance.main]
      bucket = "dev-tf-test-bucket-3007"
      tags = {
      Name        = "My bucket"
      Environment = "Dev"
   }
   }
Now visualize the entire dependency tree:
```bash
terraform graph | dot -Tpng > graph.png
```
If you don't have `dot` (Graphviz) installed, use:
```bash
terraform graph
```
and paste the output into an online Graphviz viewer.

**Document:** When would you use `depends_on` in real projects? Give two examples.
depends_on is used to explicitly define dependencies when Terraform cannot infer them automatically through resource references.

Example 1:EC2 needs internet access, But EC2 does NOT directly reference Internet Gateway. So we can use depends_on to ensure Internet Gateway is created before EC2.

Example 2: Null resource that runs a script after an EC2 instance is created. The null resource doesn't have any direct reference to the EC2 instance, so we use depends_on to ensure it runs after the instance is up.


### Task 6: Lifecycle Rules and Destroy
1. Add a `lifecycle` block to your EC2 instance:
```hcl
lifecycle {
  create_before_destroy = true
}
```
2. Change the AMI ID to a different one and run `terraform plan` -- observe that Terraform plans to create the new instance before destroying the old one

3. Destroy everything:
```bash
terraform destroy
```
4. Watch the destroy order -- Terraform destroys in reverse dependency order. Verify in the AWS console that everything is cleaned up.
   aws_route_table_association.main: Destroying... [id=rtbassoc-0e062d7a08d59d863]
   aws_vpc_security_group_ingress_rule.main: Destroying... [id=sgr-01ff1edf5b1bf5a22]
   aws_vpc_security_group_egress_rule.example: Destroying... [id=sgr-023d5a40be6770cb4]
   aws_vpc_security_group_ingress_rule.main1: Destroying... [id=sgr-046422058996203e9]
   aws_s3_bucket.name: Destroying... [id=dev-tf-test-bucket-3007]
   aws_vpc_security_group_egress_rule.example: Destruction complete after 2s
   aws_vpc_security_group_ingress_rule.main1: Destruction complete after 2s
   aws_route_table_association.main: Destruction complete after 2s
   aws_vpc_security_group_ingress_rule.main: Destruction complete after 2s
   aws_s3_bucket.name: Destruction complete after 2s
   aws_route_table.main: Destroying... [id=rtb-07bf4f3fb06a2fd8d]
   aws_instance.main: Destroying... [id=i-03927d557b7517cbd]
   aws_route_table.main: Destruction complete after 1s
   aws_internet_gateway.main: Destroying... [id=igw-0d49fe8f5ed5f20be]
   aws_instance.main: Still destroying... [id=i-03927d557b7517cbd, 00m10s elapsed]
   aws_internet_gateway.main: Still destroying... [id=igw-0d49fe8f5ed5f20be, 00m10s elapsed]
   aws_instance.main: Still destroying... [id=i-03927d557b7517cbd, 00m20s elapsed]
   aws_internet_gateway.main: Destruction complete after 20s
   aws_instance.main: Still destroying... [id=i-03927d557b7517cbd, 00m30s elapsed]
   aws_instance.main: Destruction complete after 32s
   aws_security_group.main: Destroying... [id=sg-080d60e3d99079f07]
   aws_subnet.main: Destroying... [id=subnet-0d82915890dc117bc]
   aws_subnet.main: Destruction complete after 1s
   aws_security_group.main: Destruction complete after 2s
   aws_vpc.main: Destroying... [id=vpc-0ea1d85b84fbc6ac8]
   aws_vpc.main: Destruction complete after 1s

**Document:** What are the three lifecycle arguments (`create_before_destroy`, `prevent_destroy`, `ignore_changes`) and when would you use each?

   create_before_destroy → Create new resource before deleting old one to avoid downtime
   prevent_destroy → Protect resource from accidental deletion (Terraform will throw error)
   ignore_changes → Ignore specific changes so Terraform doesn’t update the resource unnecessarily
---

## Hints
- `aws_vpc.main.id` syntax: `<resource_type>.<resource_name>.<attribute>`
- Use `terraform fmt` to keep your HCL clean
- CIDR `10.0.0.0/16` gives you 65,536 IPs, `10.0.1.0/24` gives you 256
- If you cannot SSH into the instance, check: security group rules, public IP, route table, internet gateway
- `terraform graph` outputs DOT format -- paste it into webgraphviz.com if you don't have Graphviz
- Always destroy resources when done to avoid AWS charges
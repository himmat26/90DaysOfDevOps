## Challenge Tasks

### Task 1: Extract Variables
Take your Day 62 infrastructure config and refactor it:

1. Create a `variables.tf` file with input variables for:
   - `region` (string, default: your preferred region)
     variable "region" {
        type        = string
        description = "AWS region for the resources"
        default     = "eu-north-1"
     }

   - `vpc_cidr` (string, default: `"10.0.0.0/16"`)
        variable "vpc_cidr" {
            type        = string
            description = "CIDR block for the VPC"
            default     = "10.0.0.0/16"
        }
   - `subnet_cidr` (string, default: `"10.0.1.0/24"`)
        variable "subnet_cidr" {
            type        = string
            description = "CIDR block for the subnet"
            default     = "10.0.1.0/24"
        }
   - `instance_type` (string, default: `"t2.micro"`)
        variable "instance_type" {
            type        = string
            description = "EC2 instance type for the web server"
            default     = "t3.micro"
        }
   - `project_name` (string, no default -- force the user to provide it)
        variable "project_name" {
            type        = string
            description = "Name of the project"
        }
   - `environment` (string, default: `"dev"`)
        variable "environment" {
            type        = string
            description = "Environment for the project"
            default     = "dev"
        }
   - `allowed_ports` (list of numbers, default: `[22, 80, 443]`)
        variable "allowed_ports" {
            type        = list(number)
            description = "List of allowed ports for the security group"
            default     = [22, 80, 443]
        }
   - `extra_tags` (map of strings, default: `{}`)
        variable "extra_tags" {
            type        = map(string)
            description = "Extra tags to apply to resources"
            default     = {}
        }

2. Replace every hardcoded value in `main.tf` with `var.<name>` references
        #resources
        resource "aws_vpc" "main" {
        cidr_block = var.vpc_cidr
        tags = {
            Name = "${var.project_name}-VPC"
        }
        }

        resource "aws_subnet" "main" {
        vpc_id                  = aws_vpc.main.id
        cidr_block              = var.subnet_cidr
        map_public_ip_on_launch = true
        tags = {
            Name = "${var.project_name}-Subnet"
        }
        }

        resource "aws_internet_gateway" "main" {
        vpc_id = aws_vpc.main.id
        tags = {
            Name = "${var.project_name}-IG"
        }
        }

        resource "aws_route_table" "main" {
        vpc_id = aws_vpc.main.id

        route {
            cidr_block = "0.0.0.0/0"
            gateway_id = aws_internet_gateway.main.id
        }
        tags = {
            Name = "${var.project_name}-Route"
        }
        }

        resource "aws_route_table_association" "main" {
        subnet_id      = aws_subnet.main.id
        route_table_id = aws_route_table.main.id
        }

        resource "aws_security_group" "main" {
        name        = "${var.project_name}-SG"
        description = "Allow TLS inbound traffic and all outbound traffic"
        vpc_id      = aws_vpc.main.id

        tags = {
            Name = "${var.project_name}-SG"
        }
        }

        resource "aws_vpc_security_group_ingress_rule" "main" {
        security_group_id = aws_security_group.main.id
        cidr_ipv4         = "0.0.0.0/0"
        from_port         = var.allowed_ports[0]
        ip_protocol       = "tcp"
        to_port           = var.allowed_ports[0]
        }

        resource "aws_vpc_security_group_ingress_rule" "main1" {
        security_group_id = aws_security_group.main.id
        cidr_ipv4         = "0.0.0.0/0"
        from_port         = var.allowed_ports[1]
        ip_protocol       = "tcp"
        to_port           = var.allowed_ports[1]
        }

        resource "aws_vpc_security_group_egress_rule" "example" {
        security_group_id = aws_security_group.main.id
        cidr_ipv4         = "0.0.0.0/0"
        from_port         = 0
        ip_protocol       = "tcp"
        to_port           = 0
        }

        data "aws_ami" "main" {
        most_recent = true
        owners      = ["amazon"]
        filter {
            name   = "name"
            values = ["ubuntu/images/hvm-ssd-gp3/ubuntu-noble-24.04-amd64-server-*"]
        }
        }

        resource "aws_instance" "main" {
        ami                         = data.aws_ami.main.id
        instance_type               = var.instance_type
        subnet_id                   = aws_subnet.main.id
        vpc_security_group_ids      = [aws_security_group.main.id]
        associate_public_ip_address = true
        lifecycle {
            create_before_destroy = true
        }
        tags = merge({
            Name = "${var.project_name}-Server"
        }, var.extra_tags)
        }

        resource "aws_s3_bucket" "name" {
        depends_on = [aws_instance.main]
        bucket     = "dev-tf-test-bucket-3007"
        tags = {
            Name        = "My bucket"
            Environment = "Dev"
        }
        }


3. Run `terraform plan` -- it should prompt you for `project_name` since it has no default
    PS C:\Users\himmats\Learning\Linux\Terraform-aws-project> terraform plan
    var.project_name
        Name of the project

        Enter a value: TerraWeek


**Document:** What are the five variable types in Terraform? (`string`, `number`, `bool`, `list`, `map`)
    1. string → A sequence of characters, used for text values (e.g., "dev", "t2.micro")
    2. number → Numeric values, can be integers or floats (e.g., 80, 3.14)
    3. bool → Boolean values, either true or false (e.g., true, false)
    4. list → An ordered collection of values, all of the same type (e.g., ["dev", "prod"], [22, 80, 443])
    5. map → A collection of key-value pairs, where keys are strings and values can be of any type (e.g., {env = "dev", owner = "Alice"})   

---

### Task 2: Variable Files and Precedence
1. Create `terraform.tfvars`:
```hcl
project_name = "terraweek"
environment  = "dev"
instance_type = "t2.micro"
```

2. Create `prod.tfvars`:
```hcl
project_name = "terraweek"
environment  = "prod"
instance_type = "t3.small"
vpc_cidr     = "10.1.0.0/16"
subnet_cidr  = "10.1.1.0/24"
```

3. Apply with the default file:
```bash
terraform plan                              # Uses terraform.tfvars automatically
```

4. Apply with the prod file:
```bash
terraform plan -var-file="prod.tfvars"      # Uses prod.tfvars
```

5. Override with CLI:
```bash
terraform plan -var="instance_type=t2.nano"  # CLI overrides everything
```

6. Set an environment variable:
```bash
export TF_VAR_environment="staging"
terraform plan                              # env var overrides default but not tfvars
```

**Document:** Write the variable precedence order from lowest to highest priority.
    1. Default values in variable blocks
    2. Environment variables (TF_VAR_...)
    3. terraform.tfvars
    4. *.auto.tfvars
    5. -var-file
    6. -var (highest priority)

*Default values < Environment variables < terraform.tfvars < .auto.tfvars < -var-file < -var

---

### Task 3: Add Outputs
Create an `outputs.tf` file with outputs for:

1. `vpc_id` -- the VPC ID
2. `subnet_id` -- the public subnet ID
3. `instance_id` -- the EC2 instance ID
4. `instance_public_ip` -- the public IP of the EC2 instance
5. `instance_public_dns` -- the public DNS name
6. `security_group_id` -- the security group ID

    Output:
    output "vpc_id" {
        description = "ID of the VPC"
        value       = aws_vpc.main.id
    }

    output "subnet_id" {
        description = "ID of the subnet"
        value       = aws_subnet.main.id
    }

    output "instance_id" {
        description = "ID of the EC2 instance"
        value       = aws_instance.main.id
    }

    output "instance_public_ip" {
        description = "Public IP address of the EC2 instance"
        value       = aws_instance.main.public_ip
    }

    output "instance_public_dns" {
        description = "Public DNS of the EC2 instance"
        value       = aws_instance.main.public_dns
    }

    output "security_group_id" {
        description = "ID of the security group"
        value       = aws_security_group.main.id
    }

Apply your config and verify the outputs are printed at the end:
```bash
terraform apply

O/P:
Apply complete! Resources: 11 added, 0 changed, 0 destroyed.

Outputs:

instance_id = "i-01efedfe1d2195ded"
instance_public_dns = ""
instance_public_ip = "51.20.128.39"
security_group_id = "sg-0c50fae5649b1fe9b"
subnet_id = "subnet-0f33465ac9dc6c198"
vpc_id = "vpc-04feefb9980ac4914"


# After apply, you can also run:
terraform output                          # Show all outputs
    instance_id = "i-01efedfe1d2195ded"
    instance_public_dns = ""
    instance_public_ip = "51.20.128.39"
    security_group_id = "sg-0c50fae5649b1fe9b"
    subnet_id = "subnet-0f33465ac9dc6c198"
    vpc_id = "vpc-04feefb9980ac4914"

terraform output instance_public_ip       # Show a specific output
    "51.20.128.39"
terraform output -json                    # JSON format for scripting
    
    Output:
    {
    "instance_id": {
        "sensitive": false,
        "type": "string",
        "value": "i-01efedfe1d2195ded"
    },
    "instance_public_dns": {
        "sensitive": false,
        "type": "string",
        "value": ""
    },
    "instance_public_ip": {
        "sensitive": false,
        "type": "string",
        "value": "51.20.128.39"
    },
    "security_group_id": {
        "sensitive": false,
        "type": "string",
        "value": "sg-0c50fae5649b1fe9b"
    },
    "subnet_id": {
        "sensitive": false,
        "type": "string",
        "value": "subnet-0f33465ac9dc6c198"
    },
    "vpc_id": {
        "sensitive": false,
        "type": "string",
        "value": "vpc-04feefb9980ac4914"
    }
    }
```

**Verify:** Does `terraform output instance_public_ip` return the correct IP?

---

### Task 4: Use Data Sources
Stop hardcoding the AMI ID. Use a data source to fetch it dynamically.

1. Add a `data "aws_ami"` block that:
   - Filters for Amazon Linux 2 images
   - Filters for `hvm` virtualization and `gp2` root device
   - Uses `owners = ["amazon"]`
   - Sets `most_recent = true`
    
    data "aws_ami" "amazon_linux" {
        most_recent = true
        owners      = ["amazon"]

        filter {
            name   = "root-device-type"
            values = ["ebs"]
        }

        filter {
            name   = "virtualization-type"
            values = ["hvm"]
        }
    }

2. Replace the hardcoded AMI in your `aws_instance` with `data.aws_ami.amazon_linux.id`
    ami = data.aws_ami.amazon_linux.id

3. Add a `data "aws_availability_zones"` block to fetch available AZs in your region
    data "aws_availability_zones" "available" {
     state = "available"
    }

4. Use the first AZ in your subnet: `data.aws_availability_zones.available.names[0]`
     availability_zone = data.aws_availability_zones.available.names[0]

Apply and verify -- your config now works in any region without changing the AMI.

    Plan: 1 to add, 0 to change, 1 to destroy.

    Changes to Outputs:
    ~ instance_id         = "i-01efedfe1d2195ded" -> (known after apply)
    ~ instance_public_dns = null -> (known after apply)
    ~ instance_public_ip  = "51.20.128.39" -> (known after apply)

**Document:** What is the difference between a `resource` and a `data` source?
- A `resource` block creates and manages infrastructure components (e.g., VPCs, EC2 instances) -- it defines what Terraform should create, update, or delete.
- A `data` source block is read-only and is used to fetch information about existing resources (e.g., AMI IDs, availability zones).

---

### Task 5: Use Locals for Dynamic Values
1. Add a `locals` block:
```hcl
locals {
  name_prefix = "${var.project_name}-${var.environment}"
  common_tags = {
    Project     = var.project_name
    Environment = var.environment
    ManagedBy   = "Terraform"
  }
}
``` 

2. Replace all Name tags with `local.name_prefix`:
   - VPC: `"${local.name_prefix}-vpc"`
   - Subnet: `"${local.name_prefix}-subnet"`
   - Instance: `"${local.name_prefix}-server"`

3. Merge common tags with resource-specific tags:
```hcl
tags = merge(local.common_tags, {
  Name = "${local.name_prefix}-server"
})
```

Apply and check the tags in the AWS console -- every resource should have consistent tagging.
    # aws_vpc.main will be updated in-place
    ~ resource "aws_vpc" "main" {
          id                                   = "vpc-04feefb9980ac4914"
        ~ tags                                 = {
            ~ "Name" = "terraweek-VPC" -> "terraweek-dev-VPC"
         }
        ~ tags_all                             = {
            ~ "Name" = "terraweek-VPC" -> "terraweek-dev-VPC"
            }
            # (19 unchanged attributes hidden)

---

### Task 6: Built-in Functions and Conditional Expressions
Practice these in `terraform console`:
```bash
terraform console
```

1. **String functions:**
   - `upper("terraweek")` -> `"TERRAWEEK"`
   - `join("-", ["terra", "week", "2026"])` -> `"terra-week-2026"`
   - `format("arn:aws:s3:::%s", "my-bucket")`

2. **Collection functions:**
   - `length(["a", "b", "c"])` -> `3`
   - `lookup({dev = "t2.micro", prod = "t3.small"}, "dev")` -> `"t2.micro"`
   - `toset(["a", "b", "a"])` -> removes duplicates

3. **Networking function:**
   - `cidrsubnet("10.0.0.0/16", 8, 1)` -> `"10.0.1.0/24"`

4. **Conditional expression** -- add this to your config:
```hcl
instance_type = var.environment == "prod" ? "t3.small" : "t2.micro"
```

Apply with `environment = "prod"` and verify the instance type changes.

**Document:** Pick five functions you find most useful and explain what each does.
    1. upper() → Converts a string to uppercase (e.g., "terraweek" becomes "TERRAWEEK")
    2. join() → Concatenates a list of strings using a specified separator (e.g., join("-", ["terra", "week"]) results in "terra-week")
    3. format() → Formats a string using placeholders (e.g., format("arn:aws:s3:::%s", "my-bucket") results in "arn:aws:s3:::my-bucket")
    4. length() → Returns the number of elements in a list or characters in a string (e.g., length(["a", "b", "c"]) returns 3)
    5. lookup() → Retrieves a value from a map based on a key, with an optional default (e.g., lookup({dev = "t2.micro", prod = "t3.small"}, "dev") returns "t2.micro") 

---

## Challenge Tasks

### Task 1: Inspect Your Current State
Use your Day 63 config (or create a small config with a VPC and EC2 instance). Apply it and then explore the state:

```bash
terraform show                                    # Full state in human-readable format
terraform state list                              # All resources tracked by Terraform
    data.aws_ami.main
    aws_instance.main
    aws_internet_gateway.main
    aws_route_table.main
    aws_route_table_association.main
    aws_s3_bucket.name
    aws_security_group.main
    aws_subnet.main
    aws_vpc.main
    aws_vpc_security_group_egress_rule.example
    aws_vpc_security_group_ingress_rule.main
    aws_vpc_security_group_ingress_rule.main1

terraform state show aws_instance.<name>          # Every attribute of the instance
terraform state show aws_vpc.<name>               # Every attribute of the VPC
```

Answer:
1. How many resources does Terraform track?
    Terraform tracks 12 resources in total, including data sources and managed resources.
2. What attributes does the state store for an EC2 instance? (hint: way more than what you defined)
    The state stores a wide range of attributes for the EC2 instance, including:
    - ami
    - arn
    - associate_public_ip_address
    - availability_zone
    - cpu_core_count
    - cpu_threads_per_core
    - disable_api_termination
    - ebs_block_device
    - ephemeral_block_device
    - get_password_data
    - host_id
    - iam_instance_profile
    - id
    - instance_state
    - instance_type
    - ipv6_address_count
    - ipv6_addresses
    - key_name
    - monitoring
    - network_interface
    - network_interface_id
    - network_interface_mtu
    - network_interface_owner_id
    - network_interface_private_dns_name
    - network_interface_private_ip_address
    - network_interface_security_group_id
    - network_interface_subnet_id
    - network_interface_vpc_id
    - placement_group
    - primary_network_interface_id
    - private_dns_name
    - private_ip_address
    - public_dns_name
    - public_ip_address
3. Open `terraform.tfstate` in an editor -- find the `serial` number. What does it represent?
    The `serial` number in the `terraform.tfstate` file represents the version of the state file. It is incremented every time a change is made to the infrastructure and the state file is updated. This helps Terraform keep track of changes and ensures that it is working with the most up-to-date state when planning and applying changes.

---

### Task 2: Set Up S3 Remote Backend
Storing state locally is dangerous -- one deleted file and you lose everything. Time to move it to S3.

1. First, create the backend infrastructure (do this manually or in a separate Terraform config):
```bash
# Create S3 bucket for state storage
    resource "aws_s3_bucket" "main" {
       bucket = "terraweek-state-hawk01345"

        tags = {
        Name        = "My bucket"
        Environment = "Dev"
        }
    }


# Enable versioning (so you can recover previous state)
    resource "aws_s3_bucket_versioning" "versioning_example" {
        bucket = aws_s3_bucket.main.id
        versioning_configuration {
            status = "Enabled"
        }
    }

# Create DynamoDB table for state locking
    resource "aws_dynamodb_table" "main" {
        name         = "terraweek-state-lock-hawk01345"
        billing_mode = "PAY_PER_REQUEST"
        hash_key     = "LockID"
        attribute {
        name = "LockID"
            type = "S"
        }
    }

2. Add the backend block to your Terraform config:
    terraform {
    backend "s3" {
        bucket         = "terraweek-state-hawk01345"
        key            = "dev/terraform.tfstate"
        region         = "eu-north-1"
        dynamodb_table = "terraweek-state-lock-hawk01345"
        encrypt        = true
    }
    }
    
3. Run:
```bash
terraform init
```
Terraform will ask: "Do you want to copy existing state to the new backend?" -- say yes.

PS C:\Users\himmats\Learning\Linux\Terraform-aws-project\Remote-backend> terraform init   
Initializing the backend...
Do you want to copy existing state to the new backend?
  Pre-existing state was found while migrating the previous "local" backend to the
  newly configured "s3" backend. No existing state was found in the newly
  configured "s3" backend. Do you want to copy this state to the new "s3"
  backend? Enter "yes" to copy and "no" to start with an empty state.

  Enter a value: yes

Releasing state lock. This may take a few moments...

Successfully configured the backend "s3"! Terraform will automatically
use this backend unless the backend configuration changes.


4. Verify:
   - Check the S3 bucket -- you should see `dev/terraform.tfstate`
   - Your local `terraform.tfstate` should now be empty or gone
   - Run `terraform plan` -- it should show no changes (state migrated correctly)
        No changes. Your infrastructure matches the configuration.  


### Task 3: Test State Locking
State locking prevents two people from running `terraform apply` at the same time and corrupting the state.

1. Open **two terminals** in the same project directory
2. In Terminal 1, run:
```bash
terraform apply
```
3. While Terminal 1 is waiting for confirmation, in Terminal 2 run:
```bash
terraform plan
```
4. Terminal 2 should show a **lock error** with a Lock ID

**Document:** What is the error message? Why is locking critical for team environments?

    Acquiring state lock. This may take a few moments...
    ╷
    │ Error: Error acquiring the state lock
    │
    │ Error message: operation error DynamoDB: PutItem, https response error StatusCode: 400, RequestID:
    │ 8VTNJUGHTBV8K312SV8TCEFDF3VV4KQNSO5AEMVJF66Q9ASUAAJG, ConditionalCheckFailedException: The conditional request    
    │ failed
    │ Lock Info:
    │   ID:        9730ee12-e3cf-bb73-e5b2-1280a826016d
    │   Path:      terraweek-state-hawk01345/dev/terraform.tfstate
    │   Operation: OperationTypeApply
    │   Who:       NTNET\himmats@HIMMATS01
    │   Version:   1.14.8
    │   Created:   2026-04-11 12:56:05.0797048 +0000 UTC
    │   Info:
    │
    │
    │ Terraform acquires a state lock to protect the state from being written
    │ by multiple users at the same time. Please resolve the issue above and try
    │ again. For most commands, you can disable locking with the "-lock=false"
    │ flag, but this is not recommended.

5. After the test, if you get stuck with a stale lock:
```bash
terraform force-unlock <LOCK_ID>
```

---

### Task 4: Import an Existing Resource
Not everything starts with Terraform. Sometimes resources already exist in AWS and you need to bring them under Terraform management.

1. Manually create an S3 bucket in the AWS console -- name it `terraweek-import-test-hawk`
2. Write a `resource "aws_s3_bucket"` block in your config for this bucket (just the bucket name, nothing else)
    resource "aws_s3_bucket" "imported" {
     bucket = "terraweek-import-test-hawk"

    }
3. Import it:
```bash
terraform import aws_s3_bucket.imported terraweek-import-test-<yourname>
```
aws_s3_bucket.imported: Refreshing state... [id=terraweek-import-test-hawk]

Import successful!

The resources that were imported are shown above. These resources are now in
your Terraform state and will henceforth be managed by Terraform.

4. Run `terraform plan`:
   - If you see "No changes" -- the import was perfect
   - If you see changes -- your config does not match reality. Update your config to match, then plan again until you get "No changes"

    No changes. Your infrastructure matches the configuration.

    Terraform has compared your real infrastructure against your configuration and found no differences, so no changes  
    are needed.

5. Run `terraform state list` -- the imported bucket should now appear alongside your other resources
    aws_dynamodb_table.main
    aws_s3_bucket.imported
    aws_s3_bucket.main
    aws_s3_bucket.main1
    aws_s3_bucket_versioning.versioning_example

**Document:** What is the difference between `terraform import` and creating a resource from scratch?
    TERRAFORM IMPORT:
- Brings an existing resource under Terraform management
- Requires the resource to already exist in AWS

TERRAFORM CREATE:
- Creates a new resource from scratch based on your configuration
- The resource does not exist until you apply the config
---

### Task 5: State Surgery -- mv and rm
Sometimes you need to rename a resource or remove it from state without destroying it in AWS.

1. **Rename a resource in state:**
```bash
terraform state list                              # Note the current resource names
terraform state mv aws_s3_bucket.imported aws_s3_bucket.logs_bucket
```
Update your `.tf` file to match the new name. Run `terraform plan` -- it should show no changes.

    resource "aws_s3_bucket" "logs_bucket" {
        bucket = "terraweek-import-test-hawk"

    }
2. **Remove a resource from state (without destroying it):**
```bash
terraform state rm aws_s3_bucket.logs_bucket
```
    Removed aws_s3_bucket.logs_bucket
    Successfully removed 1 resource instance(s).
    Releasing state lock. This may take a few moments...

Run `terraform plan` -- Terraform no longer knows about the bucket, but it still exists in AWS.


3. **Re-import it** to bring it back:
```bash
terraform import aws_s3_bucket.logs_bucket terraweek-import-test-<yourname>
```
aws_s3_bucket.logs_bucket: Import prepared!
  Prepared aws_s3_bucket for import
aws_s3_bucket.logs_bucket: Refreshing state... [id=terraweek-import-test-hawk]

Import successful!

The resources that were imported are shown above. These resources are now in
your Terraform state and will henceforth be managed by Terraform.

**Document:** When would you use `state mv` in a real project? When would you use `state rm`?
    state mv:
    - When you want to rename a resource in your Terraform configuration without destroying and recreating it in AWS
    state rm:
    - When you want to remove a resource from Terraform management without deleting it in AWS (e.g., if it was created manually and you want to stop managing it with Terraform)

---

### Task 6: Simulate and Fix State Drift
State drift happens when someone changes infrastructure outside of Terraform -- through the AWS console, CLI, or another tool.

1. Apply your full config so everything is in sync
    Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
2. Go to the **AWS console** and manually:
   - Change the Name tag of your EC2 instance to `"ManuallyChanged"`
        Terraweek-manch
   - Change the instance type if it's stopped (or add a new tag)
3. Run:
```bash
terraform plan
```
You should see a **diff** -- Terraform detects that reality no longer matches the desired state.
      # aws_instance.main must be replaced
        +/- resource "aws_instance" "main" {
            ~ arn                                  = "arn:aws:ec2:eu-north-1:453634598509:instance/i-075970fb5116ae464" -> (known after apply)
            ~ associate_public_ip_address          = false -> true # forces replacement
            ~ availability_zone                    = "eu-north-1a" -> (known after apply)
4. You have two choices:    
   - **Option A:** Run `terraform apply` to force reality back to match your config (reconcile)
   - **Option B:** Update your `.tf` files to match the manual change (accept the drift)

5. Choose Option A -- apply and verify the tags are restored.
        Apply complete! Resources: 1 added, 0 changed, 1 destroyed.

6. Run `terraform plan` again -- it should show "No changes." Drift resolved.

**Document:** How do teams prevent state drift in production? (hint: restrict console access, use CI/CD for all changes)
    - Restrict direct access to the AWS console for production environments
    - Use IAM policies to limit who can make changes to critical resources
    - Implement a CI/CD pipeline where all infrastructure changes must go through Terraform and code review
    - Regularly run `terraform plan` in CI to detect any drift early

---

---
title: "Storing State"
linkTitle: "Storing State"
weight: 10
---

## Setting up S3

1. Create a separate directory
```
mkdir -p global/s3
```
2. Create the bucket
```terraform
# https://registry.terraform.io/providers/hashicorp/aws/5.4.0/docs/resources/s3_bucket
resource "aws_s3_bucket" "tf_state" {
    bucket = "bucket_name"

    lifecycle {
      prevent_destroy = true
    }
}

resource "aws_s3_bucket_versioning" "tf_state" {
    bucket = aws_s3_bucket.tf_state.id
    versioning_configuration {
      status = "Enabled"
    }
}

resource "aws_s3_bucket_server_side_encryption_configuration" "tf_state" {
    bucket = aws_s3_bucket.tf_state.id
    rule {
      apply_server_side_encryption_by_default {
        sse_algorithm = "aws:kms"
        # We'll use the aws/s3 default AWS managed master key
        # kms_master_key_id = aws_kms_key.mykey.arn
      }
    }
}
```
3. Secure the bucket
```terraform
data "aws_iam_policy_document" "secure_tf_state" {
    # Require access via TLS
    statement {
      effect  = "Deny"
      actions = ["s3:*"]
      resources = [
        aws_s3_bucket.tf_state.arn,
        "${aws_s3_bucket.tf_state.arn}/*"
      ]
      principals {
        type        = "AWS"
        identifiers = ["*"]
      }
      condition {
        test     = "Bool"
        variable = "aws:SecureTransport"
        values   = [false]
      }
    }

# Deny everyone except these IAM users
# https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_notprincipal.html
    statement {
      effect  = "Deny"
      actions = ["s3:*"]
      resources = [
        aws_s3_bucket.tf_state.arn,
        "${aws_s3_bucket.tf_state.arn}/*"
      ]
      not_principals {
        type = "AWS"
        identifiers = [
          "arn:aws:iam::<aws-account-number>:user/your-admin-user-1",
          "arn:aws:iam::<aws-account-number>:user/your-admin-user-2",
          "arn:aws:iam::<aws-account-number>:root" # Do not delete this, ever.
        ]
      }
    }

# Allow these IAM users to access the s3 functionality of this bucket
    statement {
      effect  = "Allow"
      actions = ["s3:*"]
      resources = [
        aws_s3_bucket.tf_state.arn,
        "${aws_s3_bucket.tf_state.arn}/*"
      ]
      principals {
        type = "AWS"
        identifiers = [
          "arn:aws:iam::<aws-account-number>:user/your-admin-user-1",
          "arn:aws:iam::<aws-account-number>:user/your-admin-user-2",
          "arn:aws:iam::<aws-account-number>:root" # Do not delete this, ever.
        ]
      }
    }
}

# https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/s3_bucket_policy
resource "aws_s3_bucket_policy" "secure_tf_state" {
  bucket = aws_s3_bucket.tf_state.id
  policy = data.aws_iam_policy_document.secure_tf_state.json
}
```
4. Create DynamoDB table for locking
```terraform
resource "aws_dynamodb_table" "terraform_locks" {
  name         = "terraform-locks"
  billing_mode = "PAY_PER_REQUEST"
  hash_key     = "LockID"

  attribute {
    name = "LockID"
    type = "S"
  }
}
```
5. Run `terraform init` && `terraform apply`
6. Add the s3 bucket as a backend
```terraform
terraform {
  required_version = ">= 1.5.0, < 2.0.0"

  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.4.0"
    }
  }

  backend "s3" {
    bucket         = "bucket-name"
    key            = "global/s3/terraform.tfstate" # must be unique for this "component"
    region         = "your-aws-region-of-choice"
    dynamodb_table = "terraform-locks"
    encrypt        = true
  }
}

provider "aws" {
  region = "your-aws-region-of-choice"

  # NOTE: For submodules, any added tags to resources should use this pattern so
  #       as not to replace the default_tags defined here.
  #
  # In the modules, declare a default_tags variable:
  # variable "default_tags" {
  #   type = map(string)
  #   default = {
  #     ModuleSource = "module-name"
  #     ModuleVersion = "module-git-tag"
  #     ...
  #   }
  # }
  #
  # Then in any resource in the module use:
  # tags = merge(
  #   var.default_tags,
  #   { Name = "resource_name" }
  # )
  default_tags {
    tags = {
      ManagedBy   = "terraform"
      Environment = "dev"
    }
  }
}
```
7. Make outputs available
```terraform
output "tf_state_s3_bucket_arn" {
  value = aws_s3_bucket.tf_state.arn
}

output "tf_state_locks_dynamodb_table_name" {
  value = aws_dynamodb_table.terraform_locks.name
}
```

## Isolating State

1. Create a new directory separate from the s3 bucket created in previous steps.
```sh
mkdir -p path/component
```
2. Create the backend
```terraform
terraform {
  required_version = ">= 1.5.0, < 2.0.0"

  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.4.0"
    }
  }

  backend "s3" {
    bucket         = "bucket-name" # same as "root" level bucket
    key            = "path/component/terraform.tfstate" # must be unique for this "component"
    region         = "your-aws-region-of-choice"
    dynamodb_table = "terraform-locks"
    encrypt        = true
  }
}

provider "aws" {
  region = "your-aws-region-of-choice"
  default_tags {
    tags = {
      ManagedBy   = "terraform"
      Environment = "dev"
    }
  }
}
```
3. Run `terraform init`



## References

<!-- Format for online resources: -->
<!-- Author Last Name, First Name. “Title of Work.” Title of Site, Sponsor or -->
<!-- Publisher (include only if different from website title or author), Date of -->
<!-- Publication or Update Date, URL. Accessed Date (only if no date of publication -->
<!-- or update date). -->

1. Brikman, Yevgeniy "How to manage Terraform state." Oct. 3, 2016, [URL](https://blog.gruntwork.io/how-to-manage-terraform-state-28f5697e68fa)


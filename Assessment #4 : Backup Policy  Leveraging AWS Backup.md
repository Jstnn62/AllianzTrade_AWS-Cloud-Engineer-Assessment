

provider "aws" {
  region = "eu-central-1"
}

provider "aws" {
  alias  = "dr"
  region = "eu-west-1"
}

# Backup Vault (Primary Region)

resource "aws_backup_vault" "primary_vault" {
  name = "prod-backup-vault"
}

# Backup Vault (DR Region)

resource "aws_backup_vault" "dr_vault" {
  provider = aws.dr
  name     = "prod-dr-backup-vault"
}

# Backup Plan

resource "aws_backup_plan" "daily_backup" {

  name = "daily-backup-plan"

  rule {

    rule_name         = "daily-backup"
    target_vault_name = aws_backup_vault.primary_vault.name

    schedule = "cron(0 2 * * ? *)"

    lifecycle {
      delete_after = 30
    }

    copy_action {

      destination_vault_arn = aws_backup_vault.dr_vault.arn

      lifecycle {
        delete_after = 90
      }

    }
  }
}

# IAM Role used by AWS Backup

resource "aws_iam_role" "backup_role" {

  name = "aws-backup-role"

  assume_role_policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Effect = "Allow"
        Principal = {
          Service = "backup.amazonaws.com"
        }
        Action = "sts:AssumeRole"
      }
    ]
  })
}

# Tag Based Resource Selection

resource "aws_backup_selection" "backup_resources" {

  iam_role_arn = aws_iam_role.backup_role.arn
  name         = "tag-based-backup"
  plan_id      = aws_backup_plan.daily_backup.id

  selection_tag {
    type  = "STRINGEQUALS"
    key   = "ToBackup"
    value = "true"
  }

}

# Vault Lock (WORM protection)

resource "aws_backup_vault_lock_configuration" "vault_lock" {

  backup_vault_name = aws_backup_vault.primary_vault.name

  min_retention_days = 7
  max_retention_days = 365

}

## Resource name should not repeat the type

```hcl
# bad
resource "aws_iam_role" "app_role" {}

# good
resource "aws_iam_role" "app" {}
```

## Variables order

When declare a module all variables must be in `vars.tf` file ordered alphabetically

## Policy definition

Always define IAM policies as a number of policy document - a document per each resource type.
Such documents should be ordered alphabetically and then merged:

```hcl
data "aws_iam_policy_document" "dynamodb" {
  statement {
    effect  = "Allow"
    actions = ["dynamodb:Query"]
    resources = [
      var.user_table_arn,
      "${var.user_table_arn}/index/apiKey"
    ]
  }
}

data "aws_iam_policy_document" "logs" {
  statement {
    effect = "Allow"
    actions = [
      "logs:CreateLogStream",
      "logs:PutLogEvents",
    ]
    resources = [
      "${aws_cloudwatch_log_group.logs.arn}:log-stream:*"
    ]
  }
}

data "aws_iam_policy_document" "app" {
  source_policy_documents = [
    data.aws_iam_policy_document.dynamodb.json,
    data.aws_iam_policy_document.logs.json,
  ]
}

resource "aws_iam_policy" "app" {
  path   = "/app/"
  name   = local.full_app_name
  policy = data.aws_iam_policy_document.app.json
}
```

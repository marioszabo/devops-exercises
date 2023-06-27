## AWS EC2 - IAM Roles
 
### Requirements
 
1. Running EC2 instance without any IAM roles (so you if you connect the instance and try to run AWS commands, it fails)
2. IAM role with "IAMReadOnlyAccess" policy
 
### Objectives

1. Attach a role (and if such role doesn't exists, create it) with "IAMReadOnlyAccess" policy to the EC2 instance
2. Verify you can run AWS commands in the instance
 
### Solution

#### Console

1. Go to EC2 service
2. Click on the instance to which you would like to attach the IAM role
3. Click on "Actions" -> "Security" -> "Modify IAM Role" 
4. Choose the IAM role with "IAMReadOnlyAccess" policy and click on "Save"
5. Running AWS commands now in the instance should work fine (e.g. `aws iam list-users`)


### Solution Using Terrform
```
resource "aws_iam_role" "ec2_role" {
  name               = "EC2Role"
  assume_role_policy = <<EOF
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "ec2.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
EOF
}

resource "aws_iam_role_policy_attachment" "ec2_role_attachment" {
  role       = aws_iam_role.ec2_role.name
  policy_arn = "arn:aws:iam::aws:policy/IAMReadOnlyAccess"
}

resource "aws_iam_instance_profile" "ec2_instance_profile" {
  name = aws_iam_role.ec2_role.name
  role = aws_iam_role.ec2_role.name
}

iam_instance_profile = aws_iam_instance_profile.ec2_instance_profile.name #this has to go inside your EC2 reosurce block
 ```

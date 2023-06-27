## EC2 - Create an AMI

### Requirements

One running EC2 instance

### Objectives

1. Make some changes in the operating system of your instance (create files, modify files, ...)
2. Create an AMI image from running EC2 instance
3. Launch a new instance using the custom AMI you've created

### Solution

1. Connect to your EC2 instance (ssh, console, ...)
2. Make some changes in the operating system
3. Go to EC2 service
4. Right click on the instance where you made some changes -> Image and templates ->  Create image
5. Give the image a name and click on "Create image"
6. Launch new instance and choose the image you've just created

### Solution using Terraform
```
resource "aws_ami_from_instance" "ec2_instance_ami" {
  name               = "ec2_instance_ami"
  source_instance_id = aws_instance.ec2_instance.id
}

resource "aws_instance" "ec2_instance_copy" {
    ami = aws_ami_from_instance.ec2_instance_ami.id
    instance_type = "t2.micro"
}
```

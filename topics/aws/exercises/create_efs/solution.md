## AWS - Create EFS
 
### Requirements

Two EC2 instances in different availability zones

### Objectives

1. Create an EFS with the following properties
  1. Set lifecycle management to 60 days
  2. The mode should match a use case of scaling to high levels of throughput and I/O operations per second
2. Mount the EFS in both of your EC2 instances
 
### Solution
 
1. Go to EFS console
2. Click on "Create file system"
3. Create on "customize"
  1. Set lifecycle management to "60 days since last access"
  2. Set Performance mode to "MAX I/O" due to the requirement of "Scaling to high levels of throughput"
  3. Click on "Next"
  4. Choose security group to attach (if you don't have any, create one and make sure it has a rule to allow NFS traffic) and click on "Next" until you are able to review and create it
5. SSH into your EC2 instances
  1. Run `sudo yum install -y amazon-efs-utils`
  2. Run `mkdir efs`
  3. If you go to your EFS page and click on "Attach", you can see what ways are there to mount your EFS on your instancess
    1. The command to mount the EFS should be similar to `sudo mount -t efs -o tls <EFS name>:/ efs` - copy and paste it in your ec2 instance's OS

### Soution using Terraform
```
resource "aws_efs_file_system" "efs" {
  creation_token = "my-efs"

  lifecycle_policy {
    transition_to_ia = "AFTER_60_DAYS"
  }
}

resource "aws_efs_mount_target" "mount_target1" {
  file_system_id  = aws_efs_file_system.efs.id
  subnet_id       = aws_instance.ec2_instance.subnet_id  
}

resource "aws_efs_mount_target" "mount_target2" {
  file_system_id  = aws_efs_file_system.efs.id
  subnet_id       = aws_instance.ec2_instance_2.subnet_id  
}
```

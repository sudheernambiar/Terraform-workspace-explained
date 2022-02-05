# Terraform-workspace-explained
#### Terraform workspace, Terraform variables .tfvars explained.

## Prerequisites
- An EC2 instance with role of admin. 
- Terraform installed install git.
- Installed with packages tree, git for verification and collection of data.
- cloned all contents in this repo to a single location in EC2.
- install the autocompletion with following commad. Logout and login. This will help us in autocompletion with tab.
```
$terraform -install-autocomplete
```
## Files Explained
### Get all files for a VPC creation is used here to execute this procedure. We are refering the VPC creation part to explain the different workspaces.
```
$git clone https://github.com/sudheernambiar/Terrafom-vpc-ec2-modulebased.git
Cloning into 'Terrafom-vpc-ec2-modulebased'...
remote: Enumerating objects: 24, done.
remote: Counting objects: 100% (24/24), done.
remote: Compressing objects: 100% (23/23), done.
remote: Total 24 (delta 9), reused 0 (delta 0), pack-reused 0
Receiving objects: 100% (24/24), 6.94 KiB | 3.47 MiB/s, done.
Resolving deltas: 100% (9/9), done.
```
cd to that directory
```
[root@ip-172-31-41-66 ~]# cd Terrafom-vpc-ec2-modulebased/
[root@ip-172-31-41-66 Terrafom-vpc-ec2-modulebased]# ls
datasource.tf  output.tf  README.md  variables.tf  vpc.tf
[root@ip-172-31-41-66 Terrafom-vpc-ec2-modulebased]# 
```
### Creation of .tfvars
.tfvars or terraform varriables are used to overide the current values of variables. This will automatically replcaes the values in execution, however there wont be any change in the file.

current variable file.
```
[root@ip-172-31-41-66 Terrafom-vpc-ec2-modulebased]# cat variables.tf 
variable "cidr_block" {
    default = "172.21.0.0/16"
}

variable "project" {
    default = "directlink"  
}

variable "level" {
    default = "dev"  
}

variable "owner" {
    default = "Sudheer"  
}

variable "bits" {
    default = 3 
```
Now we need to create three .tfvars file, for development, test and production with a new pattern of key value pairs(unlike usual variables in hcl language)
```
[root@ip-172-31-41-66 Terrafom-vpc-ec2-modulebased]#
touch prod.tfvars test.tfvars dev.tfvars

[root@ip-172-31-41-66 Terrafom-vpc-ec2-modulebased]# ls
datasource.tf  dev.tfvars  output.tf  prod.tfvars  README.md  test.tfvars  variables.tf  vpc.tf
```
add following contents to 
```
[root@ip-172-31-41-66 Terrafom-vpc-ec2-modulebased]# cat prod.tfvars 
cidr_block = "172.30.0.0/16"   #Subnet we provisioning for VPC
project    = "MapMe"
level      = "Prod"
owner      = "Sudheer"
bits       = 3                 #in subnetting, bits took from host side for creation of subnets
[root@ip-172-31-41-66 Terrafom-vpc-ec2-modulebased]# cat test.tfvars 
cidr_block = "172.21.0.0/16"   #Subnet we provisioning for VPC
project    = "MapMe"
level      = "Test"
owner      = "Sudheer"
bits       = 3                 #in subnetting, bits took from host side for creation of subnets
[root@ip-172-31-41-66 Terrafom-vpc-ec2-modulebased]# cat dev.tfvars 
cidr_block = "172.20.0.0/16"   #Subnet we provisioning for VPC
project    = "MapMe"
level      = "Development"
owner      = "Sudheer"
bits       = 3                 #in subnetting, bits took from host side for creation of subnets
[root@ip-172-31-41-66 Terrafom-vpc-ec2-modulebased]# 
```
###

## Execution steps.
- terraform init (to initialise with the provider)
```
$ terraform init 
```



- To identify the procedure pre flight results
```
$ terraform plan 
```
- Execute the plan (with a yes you can permit after an overview, or explicitly work it with "terraform apply -auto-approve".
```
$ terraform apply 
```
- Incase to deploy without a pre flight check.
```
$ terraform apply -auto-approve 
```
Note: In case you want to make a clean-up use "terraform destroy"
```
$ terraform destroy
```
## Observations.

## Risk Factors
-
## Summary
A handy option to use multiple environments to coordinate in a single directory.

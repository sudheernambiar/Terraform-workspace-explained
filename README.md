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
#cd Terrafom-vpc-ec2-modulebased/
# ls
datasource.tf  output.tf  README.md  variables.tf  vpc.tf
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
make sure you are inside the git cloned directory "Terrafom-vpc-ec2-modulebased"
```
#touch prod.tfvars test.tfvars dev.tfvars

# ls
datasource.tf  dev.tfvars  output.tf  prod.tfvars  README.md  test.tfvars  variables.tf  vpc.tf
```
add following contents to 
```
# cat prod.tfvars 
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

## Execution steps.
### Terraform initialisation.
- create a provider file with the region you want to specify in the file provider.tf
```
#vi provider.tf

provider "aws" {
   region = "ap-south-1"
}

```
- terraform init (to initialise with the provider)
```
$ terraform init 
```
After the terraform init the entire file system will look like following, 
```
tree -A -a
.
├── datasource.tf
├── dev.tfvars
├── .git
│   ├── branches
│   ├── config
│   ├── description
│   ├── HEAD
│   ├── hooks
│   │   ├── applypatch-msg.sample
│   │   ├── commit-msg.sample
│   │   ├── fsmonitor-watchman.sample
│   │   ├── post-update.sample
│   │   ├── pre-applypatch.sample
│   │   ├── pre-commit.sample
│   │   ├── pre-merge-commit.sample
│   │   ├── prepare-commit-msg.sample
│   │   ├── pre-push.sample
│   │   ├── pre-rebase.sample
│   │   ├── pre-receive.sample
│   │   ├── push-to-checkout.sample
│   │   └── update.sample
│   ├── index
│   ├── info
│   │   └── exclude
│   ├── logs
│   │   ├── HEAD
│   │   └── refs
│   │       ├── heads
│   │       │   └── main
│   │       └── remotes
│   │           └── origin
│   │               └── HEAD
│   ├── objects
│   │   ├── info
│   │   └── pack
│   │       ├── pack-600dab5722460e326248a344318c1885dbdabcea.idx
│   │       └── pack-600dab5722460e326248a344318c1885dbdabcea.pack
│   ├── packed-refs
│   └── refs
│       ├── heads
│       │   └── main
│       ├── remotes
│       │   └── origin
│       │       └── HEAD
│       └── tags
├── output.tf
├── prod.tfvars
├── README.md
├── .terraform
│   └── providers
│       └── registry.terraform.io
│           └── hashicorp
│               └── aws
│                   └── 3.74.0
│                       └── linux_amd64
│                           └── terraform-provider-aws_v3.74.0_x5                  #Terraform provisioner
├── .terraform.lock.hcl
├── test.tfvars
├── variables.tf
└── vpc.tf

```
### Testing of .tfvars, with terraform plan
trying test terraform variables, but see the result in + tags_all part in below, this will change as per the contents in the tf variable file.
```
# terraform plan -var-file=test.tfvars
.
.
.
.
      + instance_tenancy                     = "default"
      + ipv6_association_id                  = (known after apply)
      + ipv6_cidr_block                      = (known after apply)
      + ipv6_cidr_block_network_border_group = (known after apply)
      + main_route_table_id                  = (known after apply)
      + owner_id                             = (known after apply)
      + tags                                 = {
          + "Name"  = "MapMe-VPC-Test"
          + "Owner" = "Sudheer"
          + "env"   = "Test"
        }
      + tags_all                             = {
          + "Name"  = "MapMe-VPC-Test"
          + "Owner" = "Sudheer"
          + "env"   = "Test"
        }
    }

Plan: 18 to add, 0 to change, 0 to destroy.

Changes to Outputs:
  + private_subnet1 = (known after apply)
  + private_subnet2 = (known after apply)
  + private_subnet3 = (known after apply)
  + public_subnet1  = (known after apply)
  + public_subnet2  = (known after apply)
  + public_subnet3  = (known after apply)
  + vpc_id          = (known after apply)

```
Now if we try to work with production variables will change as per the file, 
```
#terraform plan -var-file=prod.tfvars                             #See the tags environment now changed.
.
.
.
.

     + instance_tenancy                     = "default"
      + ipv6_association_id                  = (known after apply)
      + ipv6_cidr_block                      = (known after apply)
      + ipv6_cidr_block_network_border_group = (known after apply)
      + main_route_table_id                  = (known after apply)
      + owner_id                             = (known after apply)
      + tags                                 = {
          + "Name"  = "MapMe-VPC-Prod"
          + "Owner" = "Sudheer"
          + "env"   = "Prod"
        }
      + tags_all                             = {
          + "Name"  = "MapMe-VPC-Prod"
          + "Owner" = "Sudheer"
          + "env"   = "Prod"
        }
    }

Plan: 18 to add, 0 to change, 0 to destroy.

Changes to Outputs:
  + private_subnet1 = (known after apply)
  + private_subnet2 = (known after apply)
  + private_subnet3 = (known after apply)
  + public_subnet1  = (known after apply)
  + public_subnet2  = (known after apply)
  + public_subnet3  = (known after apply)
  + vpc_id          = (known after apply)


```
### Risk factors
If we have executed production/test/development in anyway consequently the statefile(file which contains all information about the terraform deployment will destroy, which is not a favourable things to see. This will also hurt our ability to roll back or restructure.

### Terraform workspaces
In such a scenario as I explained earlier, the solution is to make multiple places for each environment to run on. So that the statefile preserves.
Creating three workspaces for production/test/development.
Note: any time you can use the -help option to identify all possible options in terraform.
```
# terraform workspace                                        #tried tab option, you can install it with terraform -install-autocomplete
delete  list    new     select  show
# terraform workspace new production
Created and switched to workspace "production"!

You're now on a new, empty workspace. Workspaces isolate their state,
so if you run "terraform plan" Terraform will not see any existing state
for this configuration.


# terraform workspace new test
Created and switched to workspace "test"!

You're now on a new, empty workspace. Workspaces isolate their state,
so if you run "terraform plan" Terraform will not see any existing state
for this configuration.


# terraform workspace new development
Created and switched to workspace "development"!

You're now on a new, empty workspace. Workspaces isolate their state,
so if you run "terraform plan" Terraform will not see any existing state
for this configuration.

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

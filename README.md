# aws-cf-templates

Project of Possible AWS CloudFormation Templates.

## Getting Started

Use the following commands once the shell has been opened to ensure the environment is correctly configured.  

```bash
$ type -f awsl

awsl () {
  source /usr/local/share/zsh/site-functions/_aws
  export AWS_ROOT_OATH_KEY=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
  alias aws-iotp="oathtool --totp --base32 ${AWS_ROOT_OATH_KEY}"
}

# Load keys and completions.
$ awsl
```

There is a load time penalty associated with the loading of CLI completions for `aws`. As such it is preferential to load AWS environment configuration through a custom shell function. In this way, only when the user intends to work with `aws` is the load time penalty incurred. This is a kind of lazy shell loading of environment configuration. 

## Helpful Commands

Please note that for the following set of `aws` commands the CLI switch and value, `--region eu-central-1`, has been dropped. This is because the region value is assumed to be set by the options nominated in `~/.aws/config`. It is in `~/.aws/config` where the definition of `--profile arcadia.digital` can be found.

### Describe Stacks

```bash
$ aws --profile arcadia.digital cloudformation describe-stacks
```

The above command seems to only return information regarding the currently active Stack.

### List Stacks

However, working with this command appears to list all previously executed CloudFormation configuration scripts that were intended to create a Stack, even if that Stack has since been destroyed. Additionally, the returned information includes values for the date and time of when each Stack was created and destroyed.

```bash
$ aws --profile arcadia.digital cloudformation list-stacks
```

### Create Stack

```bash
# Create a new Stack from a template file.
$ aws --profile arcadia.digital \
      cloudformation create-stack \
      --stack-name arcadia-digital \
      --template-body file:///path/to/aws.cf.template.json
```

### Update Stack

```bash
# Update a Stack from a template file.
$ aws --profile arcadia.digital \
      cloudformation update-stack \
      --stack-name arcadia-digital \
      --template-body file:///path/to/aws.cf.template.json
```

### Delete Stack

```bash
$ aws --profile arcadia.digital \
      cloudformation delete-stack \
      --stack-name arcadia-group
```

Progress of a Stack being destroyed can be found by viewing the CloudFormation console in the AWS UI.

### List Stack Resources

```bash
$ aws --profile arcadia.digital \
      cloudformation list-stack-resources \
      --stack-name arcadia-digital
```

### List EC2 KeyPairs

```bash
$ aws --profile arcadia.digital ec2 describe-key-pairs
```

Use this command to help identify a possible EC2 KeyPair which can be used to bind to a new EC2 instance and enable SSH access.

## Consuming Templates

### VPC and Bastion Server

#### Create a VPC

```bash
$ aws --profile arcadia.digital \
      cloudformation create-stack \
      --stack-name arcadia-digital \
      --template-body file:///`pwd`/vpc/1.vpc.json
```

#### Add a Bastion Server

```bash
$ aws --profile arcadia.digital \
      cloudformation update-stack \
      --stack-name arcadia-digital \
      --template-body file:///`pwd`/vpc/2.bs.json \
      --parameters ParameterKey=KeyName,ParameterValue=arcadia-digital-bs \
                   ParameterKey=Subnet,ParameterValue=subnet-8484f6ec \
                   ParameterKey=VPC,ParameterValue=vpc-abdf6dc3
```

### VPC with Multiple Availability Zones and Bastion Server

#### Create a VPC

Start by first creating a VPC. This VPC shall serve as the bases of the infrastructure to follow.

```bash
$ aws --profile arcadia.digital \
      cloudformation create-stack \
      --stack-name arcadia-digital \
      --template-body file:///`pwd`/vpc-az/1.vpc.json
```

#### Add a Bastion Server

Next, provision the requirements for the Bastion Server within the newly created VPC.

```bash
$ aws --profile arcadia.digital \
      cloudformation update-stack \
      --stack-name arcadia-digital \
      --template-body file:///`pwd`/vpc-az/2.bs.json \
      --parameters ParameterKey=KeyName,ParameterValue=arcadia-digital-bs \
                   ParameterKey=Subnet,ParameterValue=subnet-8484f6ec \
                   ParameterKey=VPC,ParameterValue=vpc-abdf6dc3
```

Note the use of the `update-stack` sub-command in the above snippet - `2.bs.json` configuration extends `1.vpc.json`. To find the set of available EC2 KeyPairs refer to [List EC2 KeyPairs](list-ec2-keypairs).

Additionally, the above command expects the following parameters to be provided when working with `2.bs.json`; `KeyName`, `Subnet`, `VPC`. For both the `Subnet` and `VPC`, use the value for `subnetId` and `vpcId` which are listed within Output from running the `1.vpc.json` configuration. See the section on [Describe Stacks](describe-stacks) for further information. 

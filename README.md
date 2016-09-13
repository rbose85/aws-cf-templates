# aws-cf-templates
Project of Possible AWS CloudFormation Templates

```sh
# List all Stacks in CloudFormation.
$ aws --profile arcadia.dev --region eu-central-1 cloudformation list-stacks
```

```sh
# Create a new Stack from a template file.
$ aws --profile arcadia.dev --region eu-central-1 cloudformation create-stack --stack-name arcadia-digital --template-body file:///Users/rbose85/Code/personal/aws-cf-templates/aws.cf.template.json
```

```sh
# Update a Stack from a template file.
$ aws --profile arcadia.dev --region eu-central-1 cloudformation update-stack --stack-name arcadia-digital --template-body file:///Users/rbose85/Code/personal/aws-cf-templates/aws.cf.template.json --parameters ParameterKey=KeyName,ParameterValue=arcadia-digital-bs
```

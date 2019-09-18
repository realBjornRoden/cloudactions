# Login
* The  Command-Line Interface (CLI) 
[aws-cli](https://aws.amazon.com/cli/) & 
[aws-cli-configure](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html)
* The AWS Shell 
[aws-shell](https://github.com/awslabs/aws-shell)
* IAM Best Practices
[iam-best-practices](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html)
* IAM EC2 Roles [ec2-roles](https://docs.aws.amazon.com/en_pv/AWSEC2/latest/UserGuide/iam-roles-for-amazon-ec2.html)
* IAM Job Roles [job-roles](https://docs.aws.amazon.com/en_pv/IAM/latest/UserGuide/access_policies_job-functions.html)
## Actions
1. Open a command line session using Terminal/xterm/putty or equiv
1. Prepare to configure AWS CLI
   <br><i>NB. Do not use the AWS account root user access key. The access key for the AWS account root user gives full access to all resources for all AWS services, including billing information. The permissions cannot be reduce for the AWS account root user access key.</i>
   1. Create a GROUP in the Console, such as `ec2admin`, and assign `AmazonEC2FullAccess` as Policy [create-admin-group](https://docs.aws.amazon.com/IAM/latest/UserGuide/getting-started_create-admin-group.html)
   <br>Select one or more policies to attach. Each group can have up to 10 policies attached.
   1. Create a USER in the Console, such as `ec2admin`, assign it to the GROUP, and save the `credentials.csv` file (store and keep it secret) [create-admin-user](https://docs.aws.amazon.com/IAM/latest/UserGuide/getting-started_create-admin-group.html)
   1. Set a PASSWORD for the user [aws-password](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_passwords_admin-change-user.html)
1. Run the `aws configure` command to configure the AWS CLI using the keys for the USER (`ec2admin`)
   <br><i>NB. The command prompts for: access key, secret access key, AWS Region, and output format; stores this in a profile ("default"), this is used when running  an AWS CLI command without explicitly specify another profile.</i>
    ```
    $ aws configure
    aws configure
    AWS Access Key ID [None]: AKIAYWZGLN25PCXEFF3G
    AWS Secret Access Key [None]: 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
    Default region name [None]: 
    Default output format [None]: text

   $ cat ~/.aws/credentials
   [default]
   aws_access_key_id = AKIAYWZGLN25PCXEFF3G
   aws_secret_access_key = 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
    ```
1. Verify access to AWS with the USER (`ec2admin`), for EC2 (accepted) and S3 (denied)
   ```
   $ aws ec2 describe-instances --region us-east-1
   
   $ aws s3 ls
   
   An error occurred (AccessDenied) when calling the ListBuckets operation: Access Denied
   ```
***
* Use the `aws` command to perform operations, or use the [`aws-shell`](https://github.com/awslabs/aws-shell) or the [AWS Console](https://console.aws.amazon.com/)
```
$ aws 
usage: aws [options] <command> <subcommand> [<subcommand> ...] [parameters]
To see help text, you can run:

aws help
aws <command> help
aws <command> <subcommand> help
aws: error: the following arguments are required: command

$ aws help
AWS()                                                                    AWS()



NAME
aws -

DESCRIPTION
The  AWS  Command  Line  Interface is a unified tool to manage your AWS
services.

SYNOPSIS
aws [options] <command> <subcommand> [parameters]

Use aws command help for information on a  specific  command.  Use  aws
help  topics  to view a list of available help topics. The synopsis for
each command shows its parameters and their usage. Optional  parameters
are shown in square brackets.

OPTIONS
--debug (boolean)

Turn on debug logging.

--endpoint-url (string)

Override command's default URL with the given URL.

--no-verify-ssl (boolean)

By  default, the AWS CLI uses SSL when communicating with AWS services.
For each SSL connection, the AWS CLI will verify SSL certificates. This
option overrides the default behavior of verifying SSL certificates.

--no-paginate (boolean)

Disable automatic pagination.

--output (string)

The formatting style for command output.

o json
o text
o table

--query (string)

A JMESPath query to use in filtering the response data.

--profile (string)

Use a specific profile from your credential file.

--region (string)

The region to use. Overrides config/env settings.

--version (string)

Display the version of this tool.

--color (string)

Turn on/off color output.

o on
o off
o auto

--no-sign-request (boolean)

Do  not  sign requests. Credentials will not be loaded if this argument
is provided.

--ca-bundle (string)

The CA certificate bundle to use when verifying SSL certificates. Over-
rides config/env settings.

--cli-read-timeout (int)

The  maximum socket read time in seconds. If the value is set to 0, the
socket read will be blocking and not timeout.

--cli-connect-timeout (int)

The maximum socket connect time in seconds. If the value is set  to  0,
the socket connect will be blocking and not timeout.

AVAILABLE SERVICES
...
o ec2
...
o s3
...

SEE ALSO
o aws help topics
```

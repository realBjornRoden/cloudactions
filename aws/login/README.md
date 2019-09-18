# Login
* The  Command-Line Interface (CLI) 
[aws-cli](https://aws.amazon.com/cli/) & 
[aws-cli-configure](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html)
* The AWS Shell 
[aws-shell](https://github.com/awslabs/aws-shell)
## Actions
1. Open a command line session using Terminal/xterm/putty or equiv
1. Run the `aws configure` command to configure the AWS CLI
   <br><i>NB. The command prompts for: access key, secret access key, AWS Region, and output format; stores this in a profile ("default"), this is used when running  an AWS CLI command without explicitly specify another profile.</i>
    ```
    $ aws configure
    AWS Access Key ID [None]: AKIAIOSFODNN7EXAMPLE
    AWS Secret Access Key [None]: wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
    Default region name [None]: us-east-2
    Default output format [None]: json
    $ ls -ltr

    ```
* Use the `aws` command to perform operations, or use the [AWS Console](https://console.aws.amazon.com/)
```
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
o acm

o acm-pca

o alexaforbusiness

o amplify

o apigateway

o apigatewaymanagementapi

o apigatewayv2

o application-autoscaling

o appmesh

o appstream

o appsync

o athena

o autoscaling

o autoscaling-plans

o backup

o batch

o budgets

o ce

o chime

o cloud9

o clouddirectory

o cloudformation

o cloudfront

o cloudhsm

o cloudhsmv2

o cloudsearch

o cloudsearchdomain

o cloudtrail

o cloudwatch

o codebuild

o codecommit

o codepipeline

o codestar

o cognito-identity

o cognito-idp

o cognito-sync

o comprehend

o comprehendmedical

o configservice

o configure

o connect

o cur

o datapipeline

o datasync

o dax

o deploy

o devicefarm

o directconnect

o discovery

o dlm

o dms

o docdb

o ds

o dynamodb

o dynamodbstreams

o ec2

o ecr

o ecs

o efs

o eks

o elasticache

o elasticbeanstalk

o elastictranscoder

o elb

o elbv2

o emr

o es

o events

o firehose

o fms

o fsx

o gamelift

o glacier

o globalaccelerator

o glue

o greengrass

o groundstation

o guardduty

o health

o help

o history

o iam

o importexport

o inspector

o iot

o iot-data

o iot-jobs-data

o iot1click-devices

o iot1click-projects

o iotanalytics

o iotevents

o iotevents-data

o iotthingsgraph

o kafka

o kinesis

o kinesis-video-archived-media

o kinesis-video-media

o kinesisanalytics

o kinesisanalyticsv2

o kinesisvideo

o kms

o lambda

o lex-models

o lex-runtime

o license-manager

o lightsail

o logs

o machinelearning

o macie

o managedblockchain

o marketplace-entitlement

o marketplacecommerceanalytics

o mediaconnect

o mediaconvert

o medialive

o mediapackage

o mediapackage-vod

o mediastore

o mediastore-data

o mediatailor

o meteringmarketplace

o mgh

o mobile

o mq

o mturk

o neptune

o opsworks

o opsworks-cm

o organizations

o personalize

o personalize-events

o personalize-runtime

o pi

o pinpoint

o pinpoint-email

o pinpoint-sms-voice

o polly

o pricing

o quicksight

o ram

o rds

o rds-data

o redshift

o rekognition

o resource-groups

o resourcegroupstaggingapi

o robomaker

o route53

o route53domains

o route53resolver

o s3

o s3api

o s3control

o sagemaker

o sagemaker-runtime

o sdb

o secretsmanager

o securityhub

o serverlessrepo

o servicecatalog

o servicediscovery

o ses

o shield

o signer

o sms

o snowball

o sns

o sqs

o ssm

o stepfunctions

o storagegateway

o sts

o support

o swf

o textract

o transcribe

o transfer

o translate

o waf

o waf-regional

o workdocs

o worklink

o workmail

o workspaces

o xray

SEE ALSO
o aws help topics

```

# Login
* Command-Line Interface [SDK](https://cloud.google.com/sdk/docs/)
* Google Compute Engine documentation [gcloud](https://cloud.google.com/compute/docs/)

## Actions
1. Open a command line session using Terminal/xterm/putty or equiv

1. Run the `gcould init` command to authenticate through the webui using an Account and Password
    ```
    $ gcloud init
    Welcome! This command will take you through the configuration of gcloud.
    
    Your current configuration has been set to: [default]
    
    You can skip diagnostics next time by using the following flag:
    gcloud init --skip-diagnostics
    
    Network diagnostic detects and fixes local network connection issues.
    Checking network connection...done.                                                                                                                                                 
    Reachability Check passed.
    Network diagnostic passed (1/1 checks passed).
    
    You must log in to continue. Would you like to log in (Y/n)?  y
    
    Your browser has been opened to visit:
    
    https://accounts.google.com/o/oauth2/auth?code_challenge=...
    
    To take a quick anonymous survey, run:
    $ gcloud alpha survey
    
    You are logged in as: [USER@FQDN].
    
    This account has no projects.
    
    Would you like to create one? (Y/n)?  n

    ```
* Use the `gcloud` or `gsutil` command to perform operations, or use the [Cloud Console](https://console.cloud.google.com)
```
$ LC_CTYPE=C gcloud info
Google Cloud SDK [263.0.0]
...

$ gcloud -help
NAME
gcloud - manage Google Cloud Platform resources and developer workflow

SYNOPSIS
gcloud GROUP | COMMAND [--account=ACCOUNT]
[--billing-project=BILLING_PROJECT] [--configuration=CONFIGURATION]
[--flags-file=YAML_FILE] [--flatten=[KEY,...]] [--format=FORMAT]
[--help] [--project=PROJECT_ID] [--quiet, -q]
[--verbosity=VERBOSITY; default="warning"] [--version, -v] [-h]
[--impersonate-service-account=SERVICE_ACCOUNT_EMAIL] [--log-http]
[--trace-token=TRACE_TOKEN] [--no-user-output-enabled]

DESCRIPTION
The gcloud CLI manages authentication, local configuration, developer
workflow, and interactions with the Google Cloud Platform APIs.
...

$ gsutil help
Usage: gsutil [-D] [-DD] [-h header]... [-m] [-o] [-q] [command [opts...] args...]
Available commands:
acl              Get, set, or change bucket and/or object ACLs
bucketpolicyonly Configure Bucket Policy Only (Beta)
cat              Concatenate object content to stdout
compose          Concatenate a sequence of objects into a new composite object.
config           Obtain credentials and create configuration file
cors             Get or set a CORS JSON document for one or more buckets
cp               Copy files and objects
defacl           Get, set, or change default ACL on buckets
defstorageclass  Get or set the default storage class on buckets
du               Display object size usage
hash             Calculate file hashes
help             Get help about commands and topics
hmac             CRUD operations on service account HMAC keys.
iam              Get, set, or change bucket and/or object IAM permissions.
kms              Configure Cloud KMS encryption
label            Get, set, or change the label configuration of a bucket.
lifecycle        Get or set lifecycle configuration for a bucket
logging          Configure or retrieve logging on buckets
ls               List providers, buckets, or objects
mb               Make buckets
mv               Move/rename objects
notification     Configure object change notification
perfdiag         Run performance diagnostic
rb               Remove buckets
requesterpays    Enable or disable requester pays for one or more buckets
retention        Provides utilities to interact with Retention Policy feature.
rewrite          Rewrite objects
rm               Remove objects
rsync            Synchronize content of two buckets/directories
setmeta          Set metadata on already uploaded objects
signurl          Create a signed url
stat             Display object status
test             Run gsutil unit/integration tests (for developers)
update           Update to the latest gsutil release
version          Print version info about gsutil
versioning       Enable or suspend versioning for one or more buckets
web              Set a main page and/or error page for one or more buckets
...
```

Copier template for a CloudFormation Custom Resource Provider in Python
==============================================================
It is not hard to create a custom resource in CloudFormation, it just requires you
to implement three method: create, update and delete in a Lambda. However,
the entire setup to create and maintain a custom resource is quite laborious.

This copier template will allow you to create the scaffolding for a custom
resource provider in minutes!

Out-of-the-box features include:
- deploy a custom cloudformation resource provider in less than 2 minutes
- support for semantic versioning your lambda using [git-release-tag](https://github.com/binxio/git-release-tag)
- distributing the zipfile to buckets in all AWS regions in the world
- re-recordable unit tests using [botocore stubber recorder](https://pypi.org/project/botocore-stubber-recorder/)
- deployable [AWS Codebuild](https://aws.amazon.com/codebuild/) pipeline

## getting started!
Let's say you want to create a custom resource for a Custom Domain of an AWS AppRunner service,
because it does not yet exist. To get started, type:

```shell
pip install copier
copier  https://github.com/binxio/cloudformation-custom-provider-template /tmp/cfn-app-runner-custom-domain-provider

🎤 the name of your custom resource type?
AppRunnerCustomDomain
🎤 The name of your resource provider project?
cfn-app-runner-custom-domain-provider
🎤 The name of your Python module?
cfn_app_runner_custom_domain_provider
🎤 a short description for the custom provider?
manages app runner custom domains
🎤 Python version to use
3.9
🎤 Your full name?
Mark van Holsteijn
🎤 Your email address?
mark@binx.io
🎤 the URL to git source repository?
https://github.com/binxio/cfn-app-runner-custom-domain-provider
🎤 S3 bucket region
eu-west-1
🎤 Access to lambda zip files?
   (Use arrow keys)
 » private
   public

> Running task 1 of 1: [[ ! -d .git ]] &&  ( git init &&  git add . &&  git commit -m 'initial import'  && git tag 0.0.0) || exit 0
Initialized empty Git repository in /tmp/cfn-app-runner-custom-domain-provider/.git/
[main (root-commit) b2ce863] initial import
 21 files changed, 619 insertions(+)
...
````
This creates a project with a working custom provider for the resource `AppRunnerCustomDomain`.

### running the unit tests
To run the unit tests, type:
```shell
make test
``
The unit test will test the scaffold implementation. To create unit tests
for your resource, edit the source code in `./tests/`. To implement your custom
resource, edit the source code under `./src/``


### deploy the zip file to the bucket
To copy the zip file with the source code of the AWS Lambda of the custom resource provider, type:
```shell
aws s3 mb s3://<bucket-prefix>-<bucket-region>
```
As you can see, the zipfile will be copied to a bucket name which consists of the prefix
and the region name.  This allows the zipfile to be made available for use in
all regions.

### Deploy the custom resource provider into the account
Now the zip file is available, you deploy the custom resource provider, by typing:
```shell
make deploy-provider
```
This deploys the provider as an AWS Lambda function. To configure
the run-time parameters and permissions of the Lambda change the CloudFormation
template in the directory `./cloudformation`.

### Deploy the custom resource demo
To deploy the demo CloudFormation stack using the custom resource provider, type:
```shell
make deploy-demo
```
This deploys an CloudFormation stack with an example custom resource as a CloudFormation stack.
the run-time parameters and permissions of the Lambda change the CloudFormation
template in the file `./cloudformation/demo.yaml`. Change the configuration of the custom
resource to match your implementation.


## Version your custom resource provider
To version your custom resource provider, you can use the following commands:

```text
make tag-patch-release    -  create a tag for a new patch release
make tag-minor-release    -  create a tag for a new minor release
make tag-major-release    -  create a tag for new major release
```

This will:
- run the pre-tag command in the file `./release`
- commit all outstanding changes in the workspace
- tag the commit with the new version.

To show the current version of the workspace, type:

``shell
make show-version
``
The utility [git-release-tag](https://github.com/binxio/git-release-tag)
implements this functionality.

To deploy the current version to all regions, type:

```shell
make deploy-all-regions
```
This assumes you have buckets in all regions with the defined prefix.

## re-recordable unit tests
Once you have your custom resource provider, it undoubtedly does some AWS API calls.
The [botocore stubber recorder](https://pypi.org/project/botocore-stubber-recorder/) will
allow you to create unit test by running the test against a real account. The tests
will record the actual calls and generate the stubs. To run your unit tests, type:

```shell
make test-record
```
This will run the unit tests and record the AWS calls. To run the unit tests with the
newly created stubs, type:

```shell
make test
```


## all targets
When you type `make help`, you will get a list of all of available actions you can apply.

```text
build                -  build the lambda zip file
fmt                  -  formats the source code
test                 -  run python unit tests
test-record          -  run python unit tests, while recording the boto3 calls
test-templates       -  validate CloudFormation templates

deploy               -  AWS lambda zipfile to bucket
deploy-all-regions   -  AWS lambda zipfiles to all regional buckets
undeploy-all-regions -  deletes AWS lambda zipfile of this release from all buckets in all regions

deploy-provider      -  deploys the custom provider
delete-provider      -  deletes the custom provider

deploy-pipeline      -  deploys the CI/CD deployment pipeline
delete-pipeline      -  deletes the CI/CD deployment pipeline

deploy-demo          -  deploys the demo stack
delete-demo          -  deletes the demo stack

tag-patch-release    -  create a tag for a new patch release
tag-minor-release    -  create a tag for a new minor release
tag-major-release    -  create a tag for new major release
```
## run the unit tests
To run the unit tests for the custom resource provider, type:
```shell
make test
```


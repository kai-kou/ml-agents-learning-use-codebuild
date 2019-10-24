# ml-agents-learning-use-codebuild

## Usage

```console
> aws --version
aws-cli/1.16.263 Python/2.7.16 Linux/4.9.184-linuxkit botocore/1.12.253

> aws cloudformation create-stack \
  --stack-name ml-agents-learning-use-codebuild \
  --template-body file://template.yaml \
  --capabilities CAPABILITY_IAM \
  --region <YOUR REGION> \
  --parameters '[
      {
        "ParameterKey": "ProjectName",
        "ParameterValue": "<YOUR CodeBuild Project Name>"
      },
      {
        "ParameterKey": "InputBucketName",
        "ParameterValue": "<YOUR Input Bucket Name>"
      },
      {
        "ParameterKey": "OutputBucketName",
        "ParameterValue": "<YOUR Output Bucket Name>"
      }
  ]'


> aws cloudformation describe-stacks \
  --region <YOUR REGION> \
  --stack-name ml-agents-learning-use-codebuild \
  --query "Stacks[0].StackStatus"

"CREATE_COMPLETE"


> cd <your Unity App Build Directory>
> ls
3dball.x86_64       3dball_Data         trainer_config.yaml

> zip -r 3dball.zip 3dball.x86_64 3dball_Data trainer_config.yaml


> aws s3 cp 3dball.zip s3://<YOUR Input Bucket Name>/
upload: ./3dball.zip to s3://<YOUR Input Bucket Name>/3dball.zip


# Unity ML-Agents Docker Images
# See https://github.com/kai-kou/ml-agents-build-use-codebuild

> aws codebuild start-build \
  --region <YOUR REGION> \
  --project-name <YOUR CodeBuild Project Name> \
  --buildspec-override file://buildspec.yaml \
  --image-override <YOUR AWS ACCOUNT ID>.dkr.ecr.<YOUR REGION>.amazonaws.com/<YOUR Repository Name>:<YOUR TAG NAME> \
  --source-location-override <YOUR Input Bucket Name>/3dball.zip

# Check Status
> aws codebuild batch-get-builds \
  --region <YOUR Region> \
  --ids <YOUR Codebuild Project Name>:<Project ID> \
  --query "builds[0].phases"


> mkdir logs
> aws s3 sync s3://<YOUR Output Bucket Name>/ ./logs
> tree logs
```
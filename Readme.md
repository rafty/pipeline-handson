# CloudFormation Pipeline Handson

# 前提

- AWS Account
  > region: ap-northeast-1を使用する

- AWS CLI
- Pycharm  
  - [Pycharm Communityインストール](https://www.jetbrains.com/pycharm/download/#section=mac)
  > - Plugin: AWS Cloudformation 
  > - Pipenv python 3.7.3
- Github Repository  
  > - OAuthToken

- AWS  
  > EC2 KeyPairの作成

# Cloudformation練習

```
# KeyPairの確認 無ければ作成
$ aws ec2 describe-key-pairs
```

```
# ネストテンプレートをpackageしてuploadする
$ aws cloudformation package \
--template-file master-stack.yml \
--s3-bucket rafty-test \
--output-template-file /tmp/test-packed.yml

$ aws cloudformation deploy \
--template-file /tmp/test-packed.yml \
--stack-name ecs-rsc-stack \
--capabilities CAPABILITY_NAMED_IAM \
--parameter-overrides \
System=ecs-rsc \
Stage=dev \
VPCCidrBlock=10.0.0.0/24 \
InstanceType=t2.nano \
KeyName=kdc-poc
```

---
> 以下は参考

```
$ aws cloudformation create-stack \
--stack-name ecs-rsc-pipeline \
--region ap-northeast-1 \
--template-body file://master-stack.yml \
--capabilities CAPABILITY_NAMED_IAM \
--parameters \
ParameterKey=System,ParameterValue=ecs-rsc \
ParameterKey=Stage,ParameterValue=dev \
ParameterKey=VPCCidrBlock,ParameterValue=10.0.0.0/24 \
ParameterKey=InstanceType,ParameterValue=t2.micro \
ParameterKey=KeyName,ParameterValue=kdc-poc
```

```
$ aws cloudformation update-stack \
--stack-name ecs-rsc-pipeline \
--region ap-northeast-1 \
--template-body file://master-stack.yml \
--capabilities CAPABILITY_NAMED_IAM
--parameters \
ParameterKey=System,ParameterValue=ecs-rsc \
ParameterKey=Stage,ParameterValue=dev \
ParameterKey=VPCCidrBlock,ParameterValue=10.0.0.0/24 \
ParameterKey=InstanceType,ParameterValue=t2.micro \
ParameterKey=KeyName,ParameterValue=kdc-poc
```

# pipelineの実行

```
aws cloudformation create-stack \
--stack-name ecs-rsc-pipeline \
--region ap-northeast-1 \
--template-body file://pipeline/pipeline.yml \
--capabilities CAPABILITY_NAMED_IAM \
--parameters \
ParameterKey=System,ParameterValue=ecs-rsc
```

```
aws cloudformation update-stack \
--stack-name ecs-rsc-pipeline \
--region ap-northeast-1 \
--template-body file://pipeline/pipeline.yml \
--capabilities CAPABILITY_NAMED_IAM \
--parameters \
ParameterKey=System,ParameterValue=ecs-rsc
```

# 参考
[Continuous Delivery of Nested AWS CloudFormation Stacks Using AWS CodePipeline](https://aws.amazon.com/jp/blogs/devops/continuous-delivery-of-nested-aws-cloudformation-stacks-using-aws-codepipeline/)  
![](https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2017/06/07/Pipeline_vertical_design-2-362x1024.png)
[GitHub/CodeBuild/CodePipelineを利用してCloudFormationのCI/CDパイプラインを構築する](https://dev.classmethod.jp/cloud/aws/developing-cloudformation-ci-cd-pipeline-with-github-codebuild-codepipeline/)
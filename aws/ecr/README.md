# Amazon Elastic Container Registry

## Prerequisite

- `awscli` installed
- Configured AWS credentials with `~/.aws/credentials`

## Connect to Registry

- Get password to use ecr with `aws ecr get-login-password` command.
- Login to docker

```sh
$ aws ecr get-login-password --region ${AWS_REGION} | docker login --username AWS --password-stdin $ECR_REPO
```

## Create Repository

```sh
$ aws ecr create-repository --repository-name ${IMAGE_NAME}
```

## Push Docker Image to Repository

```sh
$ docker tag ${IMAGE_NAME} ${ECR_REPO}/${IMAGE_NAME}
$ docker push ${ECR_REPO}/${IMAGE_NAME}
```

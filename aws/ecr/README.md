# Amazon Elastic Container Registry

## Prerequisite

- `awscli` installed
- Configured AWS credentials with `~/.aws/credentials`

## Connect to Registry

- Get password to use ecr with `aws ecr get-login-password` command
- Login to docker

```sh
$ aws ecr get-login-password --region ${AWS_REGION} | docker login --username AWS --password-stdin $ECR_REPO
```

## Create Repository

- Create ecr repository to upload container images with `aws ecr create-repository`

```sh
$ aws ecr create-repository --repository-name ${IMAGE_NAME}
```

## Create Docker Image

### From Dockerfile

```sh
$ docker build -f ${Dockerfile_PATH} -t ${IMAGE_NAME}:${TAG} .
```

### From Running Container

```sh
$ docker commit ${Container_ID} ${IMAGE_NAME}:${TAG}
```

## Push Docker Image to Repository

- Push local container image to ecr repository with `docker push`

```sh
$ docker tag ${IMAGE_NAME} ${ECR_REPO}/${IMAGE_NAME}
$ docker push ${ECR_REPO}/${IMAGE_NAME}
```

## List images from ecr repository

- List uploaded images from ecr repository with `aws ecr list-images` command

```sh
$ aws ecr list-images --repository-name ${IMAGE_NAME}
```

## Delete images from ecr repository

- Delete images from ecr repository with `aws ecr batch-delete-image` command

```sh
# Delete a image from repository with TAG
$ aws ecr batch-delete-image --repository-name ${IMAGE_NAME} --image-ids imageTag=${TAG}

# Delete multiple images from repository with TAG
$ aws ecr batch-delete-image --repository-name ${IMAGE_NAME} --image-ids imageTag=${TAG} imageTag=${ANOTHER_TAG}

# Delete a image from repository with DIGEST
$ aws ecr batch-delete-image --repository-name ${IMAGE_NAME} --image-ids imageDigest=${DIGEST}

```

This repository provides installation resources for the Get Kimball applications

# Application Installation

Applications will be installed to your Kubernetes cluster.

Prerequisites:

* Kimball-provided credentials for retrieving Kimball Docker images
* Kimball-provided configuration
    * Version
    * Sentry DSN
* Your own Docker image repository for private storage of these images
* One or more Kubernetes clusters in which to install the applications
* [Helm](https://helm.sh/)
* An S3 Bucket writable from an AWS-instance metadata credential set

## Installation Process

Installation consists of

* Downloading the Kimball app Docker image and uploading to your repository
* Installing the Kimball API Helm chart.

Applications can then be updated to point to the Daemonset for the Kimball API

### Steps

* Docker login with your credentials

```
docker login --username [USERNAME] --password-stdin quay.io
```

* Download the image and push to your own repository

```
KIMBALL_VERSION=[PROVIDED BY KIMBALL]
SENTRY_DSN=[PROVIDED BY KIMBALL]

LOCAL_REPOSITORY=[YOUR DOCKER REPOSITORY]
KIMBALL_IMAGE=quay.io/getkimball/api:${KIMBALL_VERSION}
LOCAL_IMAGE=${LOCAL_REPOSITORY}:${KIMBALL_VERSION}

docker pull quay.io/getkimball/api:${KIMBALL_VERSION}
docker tag ${KIMBALL_IMAGE} ${LOCAL_IMAGE}
docker push ${LOCAL_IMAGE}
```

* Install Kimball API from the Helm chart

```
SERVICE_TYPE=ClusterIP

helm repo add getkimball https://getkimball.github.io/charts/stable
helm upgrade kimball-api getkimball/kimball-api --install \
  --namespace getkimball \
  --create-namespace \
  --wait \
  --timeout 5m \
  --set image.repository="${LOCAL_REPOSITORY}" \
  --set image.tag=${KIMBALL_VERSION} \
  --set service.type=${SERVICE_TYPE} \
  --set kimball.sentry_dsn=${SENTRY_DSN} \
  --set kimball.s3_bucket=${S3_BUCKET}
```

## Post installation

The Helm installation notes will contain information for how to reach your installation.

Applications should be configured to make requests to the node-local daemonset.



## Helpful commands

### Install our Docker Quay credentials into Kubernetes

(Not recommended)

```
kubectl create secret docker-registry getkimball-quay \
    --docker-server=quay.io \
    --docker-username=... \
    --docker-password=... \
    --docker-email=...
```

Then include

```
  --set imagePullSecrets=getkimball-quay
```

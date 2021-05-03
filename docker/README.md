# Build docker image

## self php-fpm

```bash
GCP_PROJECT=
IMAGE_NAME=
IMAGE_TAG=

# Move into Dockerfile dir
cd docker/php-fpm

# Build image
docker build --pull --rm --no-cache -t gcr.io/${GCP_PROJECT}/${IMAGE_NAME}:${IMAGE_TAG} .

# Push image
docker push gcr.io/${GCP_PROJECT}/${IMAGE_NAME}:${IMAGE_TAG}

# Check image
gcloud container images list-tags gcr.io/${GCP_PROJECT}/${IMAGE_NAME}
```

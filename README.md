<p align="left">
    <a href="https://hub.docker.com/r/esailors/aws-ecr-http-proxy" alt="Pulls">
        <img src="https://img.shields.io/docker/pulls/esailors/aws-ecr-http-proxy" /></a>
    <a href="https://www.esailors.de" alt="Maintained">
        <img src="https://img.shields.io/maintenance/yes/2022.svg" /></a>

</p>

# aws-ecr-http-proxy

A very simple nginx push/pull proxy that forwards requests to AWS ECR and caches the responses locally.

This is a fork of [esailors/aws-ecr-http-proxy](https://github.com/Lotto24/aws-ecr-http-proxy) with the addition of basic auth support.

To use it, create basic auth credentials using `htpasswd` and then mount them with a docker volume.

### Example:

```sh
htpasswd -c ./files/.htpasswd username # enter password

docker run -d \
  --net host \
  -v ./files/.htpasswd:/usr/local/openresty/nginx/.htpasswd \
  -e PORT=80 \
  -e RESOLVER=8.8.8.8 \
  -e UPSTREAM=https://${AWS_ACCOUNT_ID}.dkr.ecr.us-east-1.amazonaws.com \
  -e AWS_ACCESS_KEY_ID=${AWS_ACCESS_KEY_ID} \
  -e AWS_SECRET_ACCESS_KEY=${AWS_SECRET_ACCESS_KEY} \
  -e AWS_REGION=${AWS_REGION} \
  -e CACHE_MAX_SIZE=100g \
  eladitz88/ecr-proxy:latest
```

### Configuration:
The proxy is packaged in a docker container and can be configured with following environment variables:

| Environment Variable                | Description                                    | Status                            | Default    |
| :---------------------------------: | :--------------------------------------------: | :-------------------------------: | :--------: |
| `AWS_REGION`                        | AWS Region for AWS ECR                         | Required                          |            |
| `AWS_ACCESS_KEY_ID`                 | AWS Account Access Key ID                      | Optional                          |            |
| `AWS_SECRET_ACCESS_KEY`             | AWS Account Secret Access Key                  | Optional                          |            |
| `AWS_USE_EC2_ROLE_FOR_AUTH`                  | Set this to true if we do want to use aws roles for authentication instead of providing the secret and access keys explicitly | Optional                          |            |
| `UPSTREAM`                          | URL for AWS ECR                                | Required                          |            |
| `RESOLVER`                          | DNS server to be used by proxy                 | Required                          |            |
| `PORT`                              | Port on which proxy listens                    | Required                          |            |
| `CACHE_MAX_SIZE`                    | Maximum size for cache volume                  | Optional                          |  `75g`     |
| `CACHE_KEY`                         | Cache key used for the content by nginx        | Optional                          |  `$uri`    |
| `ENABLE_SSL`                        | Used to enable SSL/TLS for proxy               | Optional                          | `false`    |
| `REGISTRY_HTTP_TLS_KEY`             | Path to TLS key in the container               | Required with TLS                 |            |
| `REGISTRY_HTTP_TLS_CERTIFICATE`     | Path to TLS cert in the container              | Required with TLS                 |            |

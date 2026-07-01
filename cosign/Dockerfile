FROM alpine:3.20

ARG IMAGE_TYPE=signed
ENV IMAGE_TYPE=${IMAGE_TYPE}

WORKDIR /app

CMD ["sh", "-c", "echo \"ci-images/cosign built from ${GITHUB_SHA:-local} (type: ${IMAGE_TYPE})\""]

# AWS S3 Proxy v2.0
# docker run -d -p 8080:80 -e AWS_REGION -e AWS_ACCESS_KEY_ID -e AWS_SECRET_ACCESS_KEY -e AWS_S3_BUCKET pottava/s3-proxy

FROM golang:1.23.2-alpine3.20 AS builder
RUN apk --no-cache add gcc musl-dev git
#RUN go get -u github.com/criblio/aws-s3-proxy
WORKDIR /go/src/
COPY . /go/src/
ARG BUILD_VERSION
ARG BUILD_DATE
ARG BUILD_REF
ARG BUILD_GOARCH
ARG BUILD_GOOS
RUN go mod download \
 && go mod verify \
 && CGO_ENABLED=0 go build \
    -ldflags '-s -w -X main.ver=${BUILD_VERSION} \
    -X main.commit=${BUILD_REF} -X main.date=${BUILD_DATE}' \
 && CGO_ENABLED=0 go build \
    -ldflags '-s -w -X main.ver=${BUILD_VERSION} \
    -X main.commit=${BUILD_REF} -X main.date=${BUILD_DATE}' -o /app

FROM alpine:3.11 AS libs
RUN apk --no-cache add ca-certificates

FROM scratch
COPY --from=libs /etc/ssl/certs/ca-certificates.crt /etc/ssl/certs/
COPY --from=builder /app /aws-s3-proxy
ENTRYPOINT ["/aws-s3-proxy"]

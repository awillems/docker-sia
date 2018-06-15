FROM golang AS builder
LABEL maintainer="kibako <aaron@amwtech.net>"

ENV GOOS linux
ENV GOARCH arm64
ENV CGO_ENABLED 0

RUN go get -d -u github.com/NebulousLabs/Sia/... && \
    cd $GOPATH/src/github.com/NebulousLabs/Sia && \
    make release

FROM alpine
LABEL maintainer="kibako <aaron@amwtech.net>"

ENV SIA_DIR /sia
ENV SIA_DATA_DIR="/sia-data"
ENV SIA_MODULES gctwhr

RUN apk --no-cache add socat

WORKDIR "$SIA_DIR"
COPY --from=builder /go/bin/siad ./
COPY --from=builder go/bin/siac ./
ENTRYPOINT socat tcp-listen:9980,reuseaddr,fork tcp:localhost:8000 & \
  ./siad \
    --modules "$SIA_MODULES" \
    --sia-directory "$SIA_DATA_DIR" \
    --api-addr "localhost:8000"

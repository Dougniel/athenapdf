FROM golang:1.10-alpine AS build
WORKDIR /go/src/github.com/arachnys/athenapdf/weaver

RUN apk add --update git
RUN go get -u github.com/golang/dep/cmd/dep

COPY Gopkg.lock Gopkg.toml ./
RUN dep ensure --vendor-only -v

COPY . ./

RUN \
  CGO_ENABLED=0 go build -v -o weaver .
  
FROM dougniel/athenapdf

ENV GIN_MODE release

RUN apt-get -yq update && apt-get -yq install --no-install-recommends \
    dumb-init \
    && apt-get -yq autoremove \
    && apt-get -yq clean \
    && rm -rf /var/lib/apt/lists/* \
    && truncate -s 0 /var/log/*log

COPY --from=build /go/src/github.com/arachnys/athenapdf/weaver /athenapdf-service/
WORKDIR /athenapdf-service/

ENV PATH /athenapdf-service/:$PATH

COPY conf/ /athenapdf-service/conf/

EXPOSE 8080

CMD ["dumb-init", "weaver"]

ENTRYPOINT ["/athenapdf-service/conf/entrypoint.sh"]
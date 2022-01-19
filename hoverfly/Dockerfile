FROM golang:1.16.12-alpine3.15 AS build-env

ARG hoverfly_version=1.3.3
ARG hoverfly_archive=/tmp/hoverfly-${hoverfly_version}
ARG source_directory=/usr/local/go/src/github.com/SpectoLabs/

ADD https://github.com/SpectoLabs/hoverfly/archive/v${hoverfly_version}.zip ${hoverfly_archive}

RUN mkdir -p ${source_directory} \
    && unzip ${hoverfly_archive} -d ${source_directory} \
    && mv ${source_directory}/hoverfly-${hoverfly_version} ${source_directory}/hoverfly

WORKDIR ${source_directory}/hoverfly

RUN cd core/cmd/hoverfly && CGO_ENABLED=0 GOOS=linux go install -ldflags "-s -w"
#RUN go install github.com/SpectoLabs/hoverfly/core/cmd/hoverfly 

FROM alpine:latest
RUN apk --no-cache add ca-certificates
COPY --from=build-env /usr/local/go/bin/hoverfly /bin/hoverfly
#VOLUMES
RUN mkdir /var/hoverfly
VOLUME [ "/var/hoverfly" ]
#Environment variables
ENV webserver_port=8500
ENV admin_port=8888
ENV simultion_filename=simulation.json
ENV log_level=info

#Entrypoint
CMD exec /bin/hoverfly -pp ${webserver_port} -ap ${admin_port} -import /var/hoverfly/${simultion_filename} -webserver -listen-on-host 0.0.0.0 -log-level ${log_level}

#Exposes the admin and webserver port
EXPOSE ${admin_port} ${webserver_port}
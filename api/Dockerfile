# syntax=docker/dockerfile:1

FROM golang:1.16

WORKDIR /app

COPY go.mod ./
COPY go.sum ./
RUN go mod download

COPY *.go ./

RUN go build -o /docker-api-sample

EXPOSE 8088

CMD [ "/docker-api-sample" ]
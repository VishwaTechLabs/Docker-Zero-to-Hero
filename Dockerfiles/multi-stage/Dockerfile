FROM golang:1.24 AS builder

WORKDIR /src
COPY . .
RUN go build -o app .

FROM alpine:latest
WORKDIR /app
COPY --from=builder /src/app .
USER 10001
CMD ["./app"]

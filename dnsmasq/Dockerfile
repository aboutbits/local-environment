FROM alpine:3.12

RUN apk update && apk --no-cache add dnsmasq

ENTRYPOINT ["dnsmasq", "-k"]
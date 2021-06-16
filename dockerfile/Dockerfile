FROM alpine:latest

RUN apk update
RUN apk add curl zsh vim

COPY hello-world.txt  /hello-world.txt
RUN echo 123 > 123.txt


ENTRYPOINT ["vi"]
CMD ["/hello-world.txt"]
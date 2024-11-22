FROM alpine:edge AS base

WORKDIR /cert

RUN apk upgrade --update-cache --available && \
    apk add openssl && \
    rm -rf /var/cache/apk/*

RUN openssl req -new -text -passout pass:abcd -subj /CN=localhost -out server.req -keyout privkey.pem

RUN openssl rsa -in privkey.pem -passin pass:abcd -out server.key

RUN openssl req -x509 -in server.req -text -key server.key -out server.crt

RUN chmod -R +x * && ls

FROM ubuntu

ENV DISPLAY :0

ENV USERNAME developer

WORKDIR /app

RUN apt update 

RUN DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
    apt-transport-https sudo \
    software-properties-common \
    wget openjdk-17-jdk \
    openjdk-17-jre libswt-gtk-4-java

RUN wget https://dbeaver.io/files/dbeaver-ce_latest_amd64.deb

RUN dpkg -i dbeaver-ce_latest_amd64.deb

# create and switch to a user
RUN echo "backus ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers
RUN useradd --no-log-init --home-dir /home/$USERNAME --create-home --shell /bin/bash $USERNAME
RUN adduser $USERNAME sudo
USER $USERNAME

WORKDIR /home/$USERNAME
COPY --from=base /cert .
CMD ["dbeaver"]

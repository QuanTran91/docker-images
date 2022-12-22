FROM ubuntu:20.04
#Author Quan Tran

#Install Open JDK 17 Jre

RUN apt-get update \
    && apt-get -y install openjdk-17-jre \
    && rm -rf /var/lib/apt/lists/*

ENV JAVA_HOME /usr/lib/jvm/java-17-openjdk-amd64
ENV PATH $JAVA_HOME/bin:$PATH
# CentOS 8
FROM quay.io/xiaofwan/jenkins:2.289.1-lts-centos

USER root

# Install plugin
COPY plugins.txt /usr/share/jenkins/ref/plugins.txt
RUN jenkins-plugin-cli -f /usr/share/jenkins/ref/plugins.txt

# Install redhat crets
ADD https://password.corp.redhat.com/legacy.crt \
    https://password.corp.redhat.com/RH-IT-Root-CA.crt \
    https://engineering.redhat.com/Eng-CA.crt \
    /etc/pki/ca-trust/source/anchors/
RUN update-ca-trust extract

# install adoptopenjdk-8-hotspot-8u282_b08-3 from adoptopenjdk.jfrog.io
# https://github.com/jenkinsci/docker/blob/551b02da4766eda1e7f71c01f1da46994620ad21/8/centos/centos8/hotspot/Dockerfile#L3
RUN cd /etc/pki/ca-trust/source/anchors && \
    openssl x509 -in RH-IT-Root-CA.crt -out RH-IT-Root-CA.der -outform der && \
    openssl x509 -in legacy.crt -out legacy.der -outform der && \
    openssl x509 -in Eng-CA.crt -out Eng-CA.der -outform der && \
    /usr/bin/keytool -importcert -noprompt -alias RH-IT-Root-CA -keystore /usr/lib/jvm/adoptopenjdk-8-hotspot/jre/lib/security/cacerts -storepass changeit -file RH-IT-Root-CA.der && \
    /usr/bin/keytool -importcert -noprompt -alias legacy -keystore /usr/lib/jvm/adoptopenjdk-8-hotspot/jre/lib/security/cacerts -storepass changeit -file legacy.der && \
    /usr/bin/keytool -importcert -noprompt -alias Eng-CA -keystore /usr/lib/jvm/adoptopenjdk-8-hotspot/jre/lib/security/cacerts -storepass changeit -file Eng-CA.der
RUN /usr/bin/keytool -keystore /usr/lib/jvm/adoptopenjdk-8-hotspot/jre/lib/security/cacerts -storepass changeit -list | grep rh-it-root-ca

USER 1001

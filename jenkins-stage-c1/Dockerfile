# RHEL 8 - 2.361.4-lts-rhel-ubi8-jdk11
FROM quay.io/xiaofwan/jenkins:2.361.4-lts-rhel-ubi8-jdk11

USER root

# Install plugin
COPY plugins.txt /usr/share/jenkins/ref/plugins.txt
RUN jenkins-plugin-cli -f /usr/share/jenkins/ref/plugins.txt

# Install redhat crets
ADD https://password.corp.redhat.com/legacy.crt \
    https://password.corp.redhat.com/RH-IT-Root-CA.crt \
    https://engineering.redhat.com/Eng-CA.crt \
    /etc/pki/ca-trust/source/anchors/
RUN update-ca-trust extract && \
    dnf install -y openssl && \
    dnf clean --disableplugin=subscription-manager all

# Java home changed to /opt/java/openjdk
# https://github.com/jenkinsci/docker/blob/5687ff55b2d0de30c72aed08ac08d140d38290bc/11/rhel/ubi8/hotspot/Dockerfile#L102
RUN cd /etc/pki/ca-trust/source/anchors && \
    openssl x509 -in RH-IT-Root-CA.crt -out RH-IT-Root-CA.der -outform der && \
    openssl x509 -in legacy.crt -out legacy.der -outform der && \
    openssl x509 -in Eng-CA.crt -out Eng-CA.der -outform der && \
    /opt/java/openjdk/bin/keytool -importcert -noprompt -alias RH-IT-Root-CA -keystore /opt/java/openjdk/lib/security/cacerts -storepass changeit -file RH-IT-Root-CA.der && \
    /opt/java/openjdk/bin/keytool -importcert -noprompt -alias legacy -keystore /opt/java/openjdk/lib/security/cacerts -storepass changeit -file legacy.der && \
    /opt/java/openjdk/bin/keytool -importcert -noprompt -alias Eng-CA -keystore /opt/java/openjdk/lib/security/cacerts -storepass changeit -file Eng-CA.der
RUN /opt/java/openjdk/bin/keytool -keystore /opt/java/openjdk/lib/security/cacerts -storepass changeit -list | grep rh-it-root-ca

USER 1001

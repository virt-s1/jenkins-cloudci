FROM jenkins/jenkins:2.263.1-lts-centos7

USER root

# Install plugin
COPY plugins.txt /usr/share/jenkins/ref/plugins.txt
RUN jenkins-plugin-cli -f /usr/share/jenkins/ref/plugins.txt

RUN yum install -y openssl

# Install redhat crets
ADD https://password.corp.redhat.com/legacy.crt \
    https://password.corp.redhat.com/RH-IT-Root-CA.crt \
    https://engineering.redhat.com/Eng-CA.crt \
    /etc/pki/ca-trust/source/anchors/
RUN update-ca-trust extract
RUN cd /etc/pki/ca-trust/source/anchors && \
    openssl x509 -in RH-IT-Root-CA.crt -out RH-IT-Root-CA.der -outform der && \
    openssl x509 -in legacy.crt -out legacy.der -outform der && \
    openssl x509 -in Eng-CA.crt -out Eng-CA.der -outform der && \
    /usr/lib/jvm/jre/bin/keytool -importcert -noprompt -alias RH-IT-Root-CA -keystore /usr/lib/jvm/jre/lib/security/cacerts -storepass changeit -file RH-IT-Root-CA.der && \
    /usr/lib/jvm/jre/bin/keytool -importcert -noprompt -alias legacy -keystore /usr/lib/jvm/jre/lib/security/cacerts -storepass changeit -file legacy.der && \
    /usr/lib/jvm/jre/bin/keytool -importcert -noprompt -alias Eng-CA -keystore /usr/lib/jvm/jre/lib/security/cacerts -storepass changeit -file Eng-CA.der
RUN /usr/lib/jvm/jre/bin/keytool -keystore /usr/lib/jvm/jre/lib/security/cacerts -storepass changeit -list | grep rh-it-root-ca

USER 1001

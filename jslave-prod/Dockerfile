FROM quay.io/fedora/fedora:33-x86_64
LABEL maintainer='xiaofwan@redhat.com'

ENV HOME=/home/jenkins \
    LANG=en_US.UTF-8

USER root
# Install redhat crets used by rcm repo
ADD https://password.corp.redhat.com/legacy.crt \
    https://password.corp.redhat.com/RH-IT-Root-CA.crt \
    /etc/pki/ca-trust/source/anchors/
RUN curl -k https://engineering.redhat.com/Eng-CA.crt -o /etc/pki/ca-trust/source/anchors/Eng-CA.crt && \
    update-ca-trust extract

# Create Google Cloud SDK repo
COPY files/google-cloud-sdk.repo /etc/yum.repos.d/

# Install headless Java
RUN dnf -y update && \
    dnf -y install \
        ansible \
        bc \
        beaker-client \
        bind-utils \
        bzip2 \
        chromium \
        curl \
        expect \
        gcc \
        gcc-c++ \
        genisoimage \
        gettext \
        git \
        gnupg \
        golang \
        google-cloud-sdk \
        java-11-openjdk-headless \
        jq \
        krb5-workstation \
        libvirt-python3 \
        lsof \
        make \
        nc \
        net-tools \
        npm \
        openssl \
        origin-clients \
        psmisc \
        procps-ng \
        python \
        python3 \
        python3-devel \
        python3-pika \
        python3-pip \
        rpm-build \
        rpmdevtools \
        rsync \
        sed \
        tar \
        virt-install \
        unzip \
        wget \
        which \
        zip && \
    dnf clean all && \
    mkdir -p /home/jenkins && \
    chown -R 1001:0 /home/jenkins && \
    chmod -R g+w /home/jenkins && \
    chmod g=u /etc/passwd /etc/group && \
    chmod -R 775 /etc/alternatives && \
    chmod -R 775 /var/lib/alternatives && \
    chmod -R 775 /usr/lib/jvm && \
    chmod 775 /usr/bin && \
    chmod 775 /usr/share/man/man1 && \
    mkdir -p /var/lib/origin && \
    chmod 775 /var/lib/origin && \    
    unlink /usr/bin/java && \
    unlink /usr/bin/jjs && \
    unlink /usr/bin/keytool && \
    unlink /usr/bin/pack200 && \
    unlink /usr/bin/rmid && \
    unlink /usr/bin/rmiregistry && \
    unlink /usr/bin/unpack200 && \
    unlink /usr/share/man/man1/java.1.gz && \
    unlink /usr/share/man/man1/jjs.1.gz && \
    unlink /usr/share/man/man1/keytool.1.gz && \
    unlink /usr/share/man/man1/pack200.1.gz && \
    unlink /usr/share/man/man1/rmid.1.gz && \
    unlink /usr/share/man/man1/rmiregistry.1.gz && \
    unlink /usr/share/man/man1/unpack200.1.gz && \
    pip install requests-credssp pywinrm pyvmomi boto boto3 openstacksdk stomp.py google-auth crcmod && \
    curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip" && \
    unzip awscliv2.zip && \
    ./aws/install && \
    rm -rf aws awscliv2.zip && \
    mkdir -p /etc/beaker

# Copy go-init source code
COPY go-init /tmp/go-init/

# Copy kerberos files
COPY files/jenkins.keytab files/krb5.conf /etc/
COPY files/client.conf /etc/beaker/

# Copy the entrypoint
ADD contrib/bin/* /usr/local/bin/

# Build go-init
WORKDIR  /tmp/go-init
RUN go build . && mv go-init /usr/bin

USER 1001

# Run the Jenkins JNLP client
ENTRYPOINT ["/usr/bin/go-init", "-main", "/usr/local/bin/run-jnlp-client"]

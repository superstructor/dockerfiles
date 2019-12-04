FROM circleci/clojure:openjdk-11-lein-2.9.1-node-browsers

# GitHub Actions must be run by the default Docker user (root).
USER root

ENV LEIN_ROOT "true"

ENV JVM_OPTS -Xmx4g

# Fix broken JDK keystore.
# Ref: https://stackoverflow.com/questions/6784463/error-trustanchors-parameter-must-be-non-empty/50103533#50103533
RUN /usr/bin/printf '\xfe\xed\xfe\xed\x00\x00\x00\x02\x00\x00\x00\x00\xe2\x68\x6e\x45\xfb\x43\xdf\xa4\xd9\x92\xdd\x41\xce\xb6\xb2\x1c\x63\x30\xd7\x92' > /etc/ssl/certs/java/cacerts && \
        /var/lib/dpkg/info/ca-certificates-java.postinst configure

# Install compiler, pip3 and awscli.
RUN apt-get update && \
        apt-get install -y build-essential python3-pip && \
        pip3 install awscli

# Install karma-cli.
RUN npm install -g karma-cli

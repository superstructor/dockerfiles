FROM circleci/clojure:openjdk-11-lein-2.9.1-node-browsers

# GitHub Actions must be run by the default Docker user (root).
USER root

RUN apt-get update && \
        apt-get install -y build-essential python3-pip && \
        pip3 install awscli

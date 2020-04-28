FROM openjdk:11-jdk-slim

MAINTAINER Isaac Johnston <isaac@superstruct.nz>

RUN apt-get -qq update && \
    apt-get -qqy install --no-install-recommends curl && \
    sh -c 'curl -sL https://deb.nodesource.com/setup_12.x | bash -' && \
    sh -c 'curl -sL https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add -' && \
    sh -c 'echo "deb https://dl.yarnpkg.com/debian/ stable main" | tee /etc/apt/sources.list.d/yarn.list' && \
    apt-get -qq update && \
    apt-get -qqy install --no-install-recommends nodejs yarn git gcc g++ make cmake xxd unzip libjavascriptcoregtk-4.0 libglib2.0-dev libzip-dev libcurl4-gnutls-dev libicu-dev && \
    sh -c 'curl -sL https://download.clojure.org/install/linux-install-1.10.1.536.sh | bash -' && \
    npm -g --unsafe-perm install lumo-cljs@1.5.0 && \
    git clone https://github.com/planck-repl/planck.git && \
    cd planck && \
    git checkout 2.25.0 && \
    ./script/build && \
    ./script/install && \
    cd .. && \
    rm -rf planck ~/.npm ~/.cljs ~/.m2

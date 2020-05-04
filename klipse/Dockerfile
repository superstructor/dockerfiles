#
# This Dockerfile generates a container environment that is suitable for building the CLJS analysis cache files for
# Klipse[1] deployments. Notably, re-frame uses it[2] in a GitHub Actions workflow for building the documentation site.
#
# [1]: https://github.com/viebel/klipse
# [2]: https://github.com/day8/re-frame/blob/master/.github/workflows/docs-workflow.yml
#
FROM openjdk:11-jdk-slim
MAINTAINER Isaac Johnston <isaac@superstruct.nz>

RUN apt-get -qq update && \
    apt-get -qqy install --no-install-recommends curl && \
    # Node.js LTS 12.x is used as a stable runtime for lumo-cljs.
    sh -c 'curl -fsSL https://deb.nodesource.com/setup_12.x | bash -' && \
    # Node.js package managers are required.
    sh -c 'curl -fsSL https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add -' && \
    sh -c 'echo "deb https://dl.yarnpkg.com/debian/ stable main" | tee /etc/apt/sources.list.d/yarn.list' && \
    
    apt-get -qq update && \
    apt-get -qqy install --no-install-recommends nodejs yarn git gcc g++ make cmake xxd unzip libjavascriptcoregtk-4.0 libglib2.0-dev libzip-dev libcurl4-gnutls-dev libicu-dev && \
    # Clojure CLI Tools
    sh -c 'curl -fsSL https://download.clojure.org/install/linux-install-1.10.1.536.sh | bash -' && \
    # Leiningen is used to resolve, download and build the classpath for dependencies that is then fed into Planck.
    cd /usr/local/bin && \
    curl -fsSLo lein https://raw.githubusercontent.com/technomancy/leiningen/2.9.3/bin/lein && \
    chmod 755 lein && \
    lein self-install && \

    cd - && \
    # lumo-cljs has self-hosted ClojureScript compatible versions of some core namespaces bundled with it, that are
    # used to generate cache files for Klipse.
    npm -g --unsafe-perm install lumo-cljs@1.5.0 && \

    # Planck is used to generate cache files for all other namespaces; e.g. re-frame, reagent etc.
    git clone https://github.com/planck-repl/planck.git && \
    cd planck && \
    git checkout 2.25.0 && \
    ./script/build && \
    ./script/install && \
    cd .. && \
    
    rm -rf planck ~/.npm ~/.cljs ~/.m2

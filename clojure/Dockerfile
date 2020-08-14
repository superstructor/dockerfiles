FROM ubuntu:20.04

ENV JVM_OPTS=-Xmx4g
ENV LEIN_ROOT=true

# Every RUN command creates an intermediate layer. Every additional layer has a massive performance
# impact on the 'Initialize containers' step in GitHub Actions, which in turn is by far the most
# significant step in duration in most GitHub Actions workflows. Therefore we execute and cleanup
# everything in a single RUN command to maximise performance.
RUN \
    # Just like RUN commands, every ENV line creates a new intermediate layer. So ENV lines are
    # reserved only for those environment variables that must persist after this RUN script has
    # completed.
    export JVM_VERSION=11 && \
    export LEIN_VERSION=2.9.4 && \
    export LEIN_INSTALL=/usr/local/bin && \
    export LEIN_SHA256SUM=80d17479d89174fa83fa1709b9c9dabf9396b9923c1020c480ab9c27a4e37d61 && \
    export LEIN_GPGKEY=20242BACBBE95ADA22D0AFD7808A33D379C806C3 && \
    export CLOJURE_VERSION=1.10.1.619 && \
    export CLOJURE_SHA256SUM=28b1652686426cdf856f83551b8ca01ff949b03bc9a533d270204d6511a8ca9d && \
    export BOOT_INSTALL=/usr/local/bin && \
    export BOOT_VERSION=2.8.3 && \
    export BOOT_SHA256SUM=0ccd697f2027e7e1cd3be3d62721057cbc841585740d0aaa9fbb485d7b1f17c3 && \
    export LUMO_VERSION=1.10.1 && \
    export KARMA_CLI_VERSION=2.0.0 && \
    export DIFF_SO_FANCY_VERSION=1.3.0 && \
    export BAT_VERSION=0.15.4 && \
    export BAT_SHA256SUM=66b5fa31e4946da9331824fba4b6a7076565fe83866f14562450a010a5112857 && \
    export FD_VERSION=8.1.1 && \
    export FD_SHA256SUM=90890739d3995ed721e2b858ef3de6c6a64d25f0eda2bbd1e136c041195d76f2 && \
    export HEXYL_VERSION=0.8.0 && \
    export HEXYL_SHA256SUM=2e85c60264161ba7b99d294e0eda1664b1df776a709286db942416e494168761 && \
    export POWERSHELL_MAJOR_VERSION=7 && \
    export POWERSHELL_VERSION=$POWERSHELL_MAJOR_VERSION.0.3 && \
    export POWERSHELL_SHA256SUM=a9c023940c77a96a9f5135309e44c93ed627983bb1a66ecf5beb42bbba54ead6 && \
    export GIT_GPGKEY=E1DD270288B4E6030699E45FA1715D88E1DF1F24 && \
    export NEOVIM_GPGKEY=9DBB0BE9366964F134855E2255F96FCF8231B6DD && \
    export CHROMIUM_VERSION=56.0.2924.0 && \
    export CHROMIUM_PACKAGE_URL="https://www.googleapis.com/download/storage/v1/b/chromium-browser-snapshots/o/Linux_x64%2F433062%2Fchrome-linux.zip?generation=1479441205933000&alt=media" && \
    export CHROMIUM_SHA256SUM=5e2e38ffbed6ff9bef287026c8d2628b12b5a43b5bf7c235bfa182e7fd5d213f && \
    export CHROMEDRIVER_VERSION=2.29 && \
    export CHROMEDRIVER_SHA256SUM=bb2cf08f2c213f061d6fbca9658fc44a367c1ba7e40b3ee1e3ae437be0f901c2 && \
    export PHANTOMJS_VERSION=2.1.1 && \
    export PHANTOMJS_SHA256SUM=86dd9a4bf4aee45f1a84c9f61cf1947c1d6dce9b9e8d2a907105da7852460d2f && \
    export DEBIAN_FRONTEND=noninteractive && \

    # Set the default timezone to Australia/Sydney.
    #echo "Australia/Sydney" > /etc/timezone

    cd /tmp && \

    apt-get update -q && \
    apt-get dist-upgrade -q -y && \

    # Install tools needed by the subsequent commands.
    apt-get install -q -y --no-install-recommends \
        ca-certificates gnupg \
        curl wget \
        unzip && \

    # Add the 'Git stable releases' PPA for the most recent releases of Git.
    #
    # Also, when adding a PPA do not depend on `software-properties-common` for
    # the `add-apt-repository` command as that adds several hundred megabytes.
    echo "deb http://ppa.launchpad.net/git-core/ppa/ubuntu focal main" > /etc/apt/sources.list.d/git-core-ubuntu-ppa-focal.list && \
    apt-key adv --keyserver keyserver.ubuntu.com --recv-keys $GIT_GPGKEY && \

    # Add the 'Neovim Unstable' PPA for 0.5.x-series releases of Neovim. The
    # 'Neovim Stable' PPA provides 0.4.x which is extremely out of date to the
    # point of being unusable.
    echo "deb http://ppa.launchpad.net/neovim-ppa/unstable/ubuntu focal main" >  /etc/apt/sources.list.d/neovim-ppa-ubuntu-unstable-focal.list && \
    apt-key adv --keyserver keyserver.ubuntu.com --recv-keys $NEOVIM_GPGKEY && \

    # Add Node.js v12.x package repository.
    curl -sL https://deb.nodesource.com/setup_12.x | bash - && \

    # Add Yarn package repository.
    echo "deb https://dl.yarnpkg.com/debian/ stable main" > /etc/apt/sources.list.d/yarn.list && \
    curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add - && \

    # Install development tooling, common editors and language runtimes.
    apt-get install -q -y --no-install-recommends \
      git jq silversearcher-ag ncdu htop rlwrap tmux openssh-client mosh \
      neovim emacs-nox \
      zsh build-essential openjdk-$JVM_VERSION\-jdk-headless nodejs yarn python3-pip \
      pngnq pngquant pngtools pngmeta pngcrush pngcheck \
      jhead jpeginfo jpegoptim jpegpixi \
      xvfb libglib2.0-0 libgconf-2-4 \
      popularity-contest && \
    rm -rf /var/lib/apt/lists/* && \

    # Install Git Large File Storage
    #
    # Required when cloning any Git repository that uses the Git LFS Specification.
    #
    # See https://github.com/git-lfs/git-lfs
    curl -s https://packagecloud.io/install/repositories/github/git-lfs/script.deb.sh | bash && \

    # Install Clojure:
    wget https://download.clojure.org/install/linux-install-$CLOJURE_VERSION.sh && \
    echo "Verifying Clojure installer checksum..." && \
    sha256sum linux-install-$CLOJURE_VERSION.sh && \
    echo "$CLOJURE_SHA256SUM *linux-install-$CLOJURE_VERSION.sh" | sha256sum -c - && \
    chmod +x linux-install-$CLOJURE_VERSION.sh && \
    ./linux-install-$CLOJURE_VERSION.sh && \
    rm -f linux-install-$CLOJURE_VERSION.sh && \
    clojure -e "(clojure-version)" && \

    # Install Leiningen:
    mkdir -p $LEIN_INSTALL && \
    # Once leiningen 2.9.5 is released replace the below git commit-based URL with the following version-based URL.
    # Leiningen 2.9.4 has a bug that was subsequently fixed but not released that causes a syntax error.
    # See technomancy/leiningen#2691
    #
    # wget -q https://raw.githubusercontent.com/technomancy/leiningen/$LEIN_VERSION/bin/lein-pkg && \
    wget -q https://raw.githubusercontent.com/technomancy/leiningen/7677dabea40a2d17a42a718ca8c7e450b09e153c/bin/lein-pkg && \
    echo "Verifying lein-pkg checksum..." && \
    sha256sum lein-pkg && \
    echo "$LEIN_SHA256SUM *lein-pkg" | sha256sum -c - && \
    mv lein-pkg $LEIN_INSTALL/lein && \
    chmod 0755 $LEIN_INSTALL/lein && \
    wget -q https://github.com/technomancy/leiningen/releases/download/$LEIN_VERSION/leiningen-$LEIN_VERSION-standalone.zip && \
    wget -q https://github.com/technomancy/leiningen/releases/download/$LEIN_VERSION/leiningen-$LEIN_VERSION-standalone.zip.asc && \
    gpg --batch --keyserver keys.openpgp.org --recv-key $LEIN_GPGKEY && \
    echo "Verifying leiningen-$LEIN_VERSION-standalone.zip PGP signature..." && \
    gpg --batch --verify leiningen-$LEIN_VERSION-standalone.zip.asc leiningen-$LEIN_VERSION-standalone.zip && \
    rm leiningen-$LEIN_VERSION-standalone.zip.asc && \
    mkdir -p /usr/share/java && \
    mv leiningen-$LEIN_VERSION-standalone.zip /usr/share/java/leiningen-$LEIN_VERSION-standalone.jar && \

    # Install Boot:
    mkdir -p $BOOT_INSTALL && \
    wget -q https://github.com/boot-clj/boot-bin/releases/download/latest/boot.sh && \
    echo "Verifying boot installer checksum..." && \
    sha256sum boot.sh && \
    echo "$BOOT_SHA256SUM *boot.sh" | sha256sum -c - && \
    mv boot.sh $BOOT_INSTALL/boot && \
    chmod 0755 $BOOT_INSTALL/boot && \

    # Install AWS CLI
    pip3 install awscli && \

    # Install bat
    #
    # A cat(1) clone with syntax highlighting and Git integration.
    #
    # To update to a new release:
    # 1. Open https://github.com/sharkdp/bat/releases
    # 2. Download bat_VERSION_amd64.deb for the desired version
    # 3. Run `sha256sum bat_VERSION_amd64.deb`
    # 4. Copy/paste the checksum to BAT_SHA256SUM in environment variables at the beginning of this RUN script
    # 5. Edit the BAT_VERSION in environment variables at the beginning of this RUN script
    wget -q https://github.com/sharkdp/bat/releases/download/v${BAT_VERSION}/bat_${BAT_VERSION}_amd64.deb && \
    echo "Verifying bat package checksum..." && \
    sha256sum bat_${BAT_VERSION}_amd64.deb && \
    echo "$BAT_SHA256SUM *bat_${BAT_VERSION}_amd64.deb" | sha256sum -c - && \
    dpkg -i bat_${BAT_VERSION}_amd64.deb && \
    rm -f bat_${BAT_VERSION}_amd64.deb && \

    # Install fd
    #
    # fd is a simple, fast and user-friendly alternative to 'find'.
    #
    # To update to a new release:
    # 1. Open https://github.com/sharkdp/fd/releases
    # 2. Download fd_VERSION_amd64.deb for the desired version
    # 3. Run `sha256sum fd_VERSION_amd64.deb`
    # 4. Copy/paste the checksum to FD_SHA256SUM in environment variables at the beginning of this RUN script
    # 5. Edit the FD_VERSION in environment variables at the beginning of this RUN script
    wget -q https://github.com/sharkdp/fd/releases/download/v${FD_VERSION}/fd_${FD_VERSION}_amd64.deb && \
    echo "Verifying fd package checksum..." && \
    sha256sum fd_${FD_VERSION}_amd64.deb && \
    echo "$FD_SHA256SUM *fd_${FD_VERSION}_amd64.deb" | sha256sum -c - && \
    dpkg -i fd_${FD_VERSION}_amd64.deb && \
    rm -f fd_${FD_VERSION}_amd64.deb && \

    # Install hexyl
    #
    # hexyl is a command-line hex viewer.
    #
    # To update to a new release:
    # 1. Open https://github.com/sharkdp/hexyl/releases
    # 2. Download hexyl_VERSION_amd64.deb for the desired version
    # 3. Run `sha256sum hexyl_VERSION_amd64.deb`
    # 4. Copy/paste the checksum to HEXYL_SHA256SUM in environment variables at the beginning of this RUN script
    # 5. Edit the HEXYL_VERSION in environment variables at the beginning of this RUN script
    wget -q https://github.com/sharkdp/hexyl/releases/download/v${HEXYL_VERSION}/hexyl_${HEXYL_VERSION}_amd64.deb && \
    echo "Verifying hexyl package checksum..." && \
    sha256sum hexyl_${HEXYL_VERSION}_amd64.deb && \
    echo "$HEXYL_SHA256SUM *hexyl_${HEXYL_VERSION}_amd64.deb" | sha256sum -c - && \
    dpkg -i hexyl_${HEXYL_VERSION}_amd64.deb && \
    rm -f hexyl_${HEXYL_VERSION}_amd64.deb && \

    # Install Chrome
    #
    # We have an Electron app that uses an older Chrome version. So we want to test with that same older version of
    # Chrome in this image. Google deletes old versions every release, so we can't get it from their official deb package
    # repository. Instead, jump through the following hoops using an example of Chrome 56.x:
    #
    # 1. Look in https://chromereleases.googleblog.com/ for the last time "56." was mentioned in a Desktop release. In
    #    this case I found https://chromereleases.googleblog.com/2017/01/stable-channel-update-for-desktop.html
    #    NOTE: I had to use '56.0.2924.77' instead of '56.0.2924.87' as '56.0.2924.87' does not return a base position
    #          in step 2.
    #
    # 2. Look up that version history ('56.0.2924.77') in the position lookup: https://omahaproxy.appspot.com/
    #    In this case it returns a 'Branch Base Position' of '433059'.
    #
    # 3. Open the continuous build archive: https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html
    #
    # 4. Click 'Linux_x64'
    #
    # 5. Paste the 'Branch Base Position' ('433059') into the 'Filter:' field at the top and wait for remote network
    #    requests to complete. Sometimes you need to decrement or increment the number until you find a match.
    #
    # 6. After waiting a long time you might get a hit; e.g. for '433062' I got:
    #      https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Linux_x64/433062/
    #
    # 7. Copy/paste the download URL for 'chrome-linux.zip'.
    wget -q -O chromium-linux.zip $CHROMIUM_PACKAGE_URL && \
    echo "Verifying Chromium package checksum..." && \
    sha256sum chromium-linux.zip && \
    echo "$CHROMIUM_SHA256SUM *chromium-linux.zip" | sha256sum -c - && \
    cd /opt && \
    unzip /tmp/chromium-linux.zip && \
    mv chrome-linux chromium-$CHROMIUM_VERSION && \
    ln -sv chromium-$CHROMIUM_VERSION chromium && \
    cd /tmp && \
    rm chromium-linux.zip && \

    # Install ChromeDriver
    #
    # ChromeDriver version MUST be the correct version for the Chrome release it is being used with.
    #
    # For recent versions of Chrome see https://chromedriver.chromium.org/downloads/version-selection
    #
    # For older versions of Chrome (e.g. '56.x') look through https://chromedriver.storage.googleapis.com/index.html
    # and find the newest release with a 'notes.txt' file that mentions the major version e.g. 'Supports Chrome v56-58'.
    wget -q https://chromedriver.storage.googleapis.com/$CHROMEDRIVER_VERSION/chromedriver_linux64.zip && \
    echo "Verifying ChromeDriver package checksum..." && \
    sha256sum chromedriver_linux64.zip && \
    echo "$CHROMEDRIVER_SHA256SUM *chromedriver_linux64.zip" | sha256sum -c - && \
    unzip chromedriver_linux64.zip && \
    rm -f chromedriver_linux64.zip && \
    mv chromedriver /usr/local/bin/chromedriver && \
    chmod +x /usr/local/bin/chromedriver && \
    chromedriver --version && \

    # Install PhantomJS
    #
    # PhantomJS is a discontinued headless browser used for automated web page interaction, such as testing. Although
    # normal unit test builds can use Chrome via Karma, unfortunately PhantomJS is required by cljs-oss/canary builds.
    wget -q -O phantomjs.tar.bz2 https://bitbucket.org/ariya/phantomjs/downloads/phantomjs-${PHANTOMJS_VERSION}-linux-x86_64.tar.bz2 && \
    echo "Verifying PhantomJS package checksum..." && \
    sha256sum phantomjs.tar.bz2 && \
    echo "$PHANTOMJS_SHA256SUM *phantomjs.tar.bz2" | sha256sum -c - && \
    mkdir -p /opt/phantomjs/${PHANTOMJS_VERSION} && \
    tar jxf phantomjs.tar.bz2 -C /opt/phantomjs/${PHANTOMJS_VERSION} && \
    rm -f phantomjs.tar.bz2 && \
    ln -s /opt/phantomjs-${PHANTOMJS_VERSION}/bin/phantomjs /usr/local/bin/phantomjs && \

    # Install PowerShell
    #
    # Windows 10 has become a surprisingly good platform for developers in recent times. Just like its important to have
    # a preferred editor handy, be it vim or emacs, it is also important to have a familiar shell available to those who
    # want to use it.
    wget -q -O powershell.tar.gz https://github.com/PowerShell/PowerShell/releases/download/v${POWERSHELL_VERSION}/powershell-${POWERSHELL_VERSION}-linux-x64.tar.gz && \
    echo "Verifying powershell package checksum..." && \
    sha256sum powershell.tar.gz && \
    echo "$POWERSHELL_SHA256SUM *powershell.tar.gz" | sha256sum -c - && \
    mkdir -p /opt/microsoft/powershell/${POWERSHELL_MAJOR_VERSION} && \
    tar zxf powershell.tar.gz -C /opt/microsoft/powershell/${POWERSHELL_MAJOR_VERSION} && \
    rm -f powershell.tar.gz && \
    chmod +x /opt/microsoft/powershell/${POWERSHELL_MAJOR_VERSION}/pwsh && \
    ln -s /opt/microsoft/powershell/${POWERSHELL_MAJOR_VERSION}/pwsh /usr/local/bin/pwsh && \

    # Start xvfb automatically:
    printf '#!/bin/sh\nXvfb :99 -screen 0 1920x1080x24 &\nexec "$@"\n' > /docker-entrypoint.sh && \
    chmod +x /docker-entrypoint.sh && \

    # Pre-install common dependencies to improve build performance:
    #echo '(defproject deps "" :dependencies [[org.clojure/clojure "1.10.1"] [org.clojure/clojurescript "1.10.773"] [thheller/shadow-cljs "2.10.19"]] :plugins [[lein-shadow "0.2.2"] [day8/lein-git-inject "0.0.14"] [lein-ancient "0.6.15"] [lein-shell "0.5.0"] [lein-pprint "1.3.2"]])' > project.clj && \
    #lein deps && \
    #rm project.clj && \

    npm -g install \
      lumo-cljs@$LUMO_VERSION \
      karma-cli@$KARMA_CLI_VERSION \
      diff-so-fancy@$DIFF_SO_FANCY_VERSION && \

    # Cleanup
    #apt-get purge -y --auto-remove gnupg wget

    echo "Done."

ENV DISPLAY :99
ENTRYPOINT ["/docker-entrypoint.sh"]
CMD ["/bin/bash"]
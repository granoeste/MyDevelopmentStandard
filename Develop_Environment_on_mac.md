# Setup Develop Environment on macOS
Java, Android, iOS, Flutter, fastlane, git, GitHub, Python, Node.js, AWS, Firebase, etc.

1. Homebrew

    Install 
    ```
    /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
    ```
    * Auto download and install Xcode xommand Line Tool

    Check
    ```
    brew -v
    ```
    > Homebrew 1.7.2
    > Homebrew/homebrew-core (git revision 9b5d; last commit 2018-08-28)
  
    ```
    xcode-select -v
    ```
    > xcode-select version 2349.

    ref. [macOS 用パッケージマネージャー — macOS 用パッケージマネージャー](https://brew.sh/index_ja)

2. bundler

    Install
    ```
    sudo gem install bundler
    ```

    Check
    ```
    bundler -v
    ```
    > Bundler version 1.16.4

3. fastlane 

    Install
    ```
    brew install fastlane
    ```

    Add path
    ```
    echo 'export PATH=$HOME/.fastlane/bin:$PATH' >> ~/.bash_profile
    ```

    Check
    ```
    fastlane --version
    ```
    
    > fastlane installation at path:
    > /Library/Ruby/Gems/2.3.0/gems/fastlane-2.102.0/bin/fastlane
    > -----------------------------
    > [✔] 🚀
    > fastlane 2.102.0

    ref. [fastlane docs](https://docs.fastlane.tools/)

4. dotenv (*Option)

    Install
    ```
    sudo gem install dotenv
    ```

5. git (*Option)

    Check preinstall version
    ```
    git --version
    ```
    > git version 2.15.1 (Apple Git-101)

    Install 
    ```
    brew install git
    ```

    Check
    ```
    git --version
    ```
    > git version 2.18.0

6. Install xcode

7. Install iOS toolchain

    Install 
    ```
    brew install libimobiledevice
    brew install ideviceinstaller
    brew install ios-deploy
    ```

8. Install cocoapods

    Install 
    ```
    brew install cocoapods
    pod setup
    ```

9.  Java 8
    
    Prepare
    ```
     brew tap homebrew/cask-versions
    ```
    
    Install 
    ```
    brew install --cask adoptopenjdk8  
    ```

    Check
    ```
    $ java -version
    openjdk version "1.8.0_212"
    OpenJDK Runtime Environment (AdoptOpenJDK)(build 1.8.0_212-b03)
    OpenJDK 64-Bit Server VM (AdoptOpenJDK)(build 25.212-b03, mixed mode)
    ```

10. Android Studio

    [Download Android Studio and SDK tools](https://developer.android.com/studio/)
    
    Install Android Studio

    Add path
    ```
    echo 'export ANDROID_HOME=$HOME/Library/Android/sdk' >> ~/.bash_profile
    echo 'export PATH=$ANDROID_HOME/tools:$PATH' >> ~/.bash_profile
    echo 'export PATH=$ANDROID_HOME/tools/bin:$PATH' >> ~/.bash_profile
    echo 'export PATH=$ANDROID_HOME/platform-tools/:$PATH' >> ~/.bash_profile
    ```

    Install package with [sdkmanager](https://developer.android.com/studio/command-line/sdkmanager)
    ```
    sdkmanager --list
    ```

    Install packages
    ```
    sdkmanager --install build-tools;28.0.2
    sdkmanager --install platform-tools
    sdkmanager --install platforms;android-28   
    sdkmanager --install emulator
    sdkmanager --install system-images;android-28;default;x86_64
    ```
    *Install other necessary packages.

11. Flutter

    Download
    ```
    cd ~/Library
    git clone -b stable https://github.com/flutter/flutter.git
    ```
    
    Add path
    ```
    echo 'export PATH=$HOME/Library/flutter/bin:$PATH' >> ~/.bash_profile
    ```

    Check
    ```
    flutter doctor -v
    ```
    
    ```
    [✓] Flutter (Channel beta, v0.6.0, on Mac OS X 10.13.6 17G65, locale ja-JP)
        • Flutter version 0.6.0 at /Users/oonishi.k/flutter
        • Framework revision 9299c02cf7 (2 weeks ago), 2018-08-16 00:35:12 +0200
        • Engine revision e3687f70c7
        • Dart version 2.1.0-dev.0.0.flutter-be6309690f

    [!] Android toolchain - develop for Android devices
        • Android SDK at /Users/oonishi.k/android
        • Android NDK location not configured (optional; useful for native profiling support)
        • ANDROID_HOME = /Users/oonishi.k/android
        ✗ Android SDK is missing command line tools; download from https://goo.gl/XxQghQ
        • Try re-installing or updating your Android SDK,
        visit https://flutter.io/setup/#android-setup for detailed instructions.

    [✓] iOS toolchain - develop for iOS devices (Xcode 9.4.1)
        • Xcode at /Applications/Xcode.app/Contents/Developer
        • Xcode 9.4.1, Build version 9F2000
        • ios-deploy 1.9.2
        • CocoaPods version 1.5.3

    [✓] Android Studio (version 3.1)
        • Android Studio at /Applications/Android Studio.app/Contents
        ✗ Flutter plugin not installed; this adds Flutter specific functionality.
        ✗ Dart plugin not installed; this adds Dart specific functionality.
        • Java version OpenJDK Runtime Environment (build 1.8.0_152-release-1024-b01)

    [!] Connected devices
        ! No devices available

    ! Doctor found issues in 2 categories.
    ```

12. ssh config for GutHub

    1. Create key pair for GitHub. (No passphrase)
        ```
        ssh-keygen -t ed25519 -N "" -f ~/.ssh/github -C foobar@example.com
        ```

    2. Register public key to GitHub

        Take public key to clipboard
        ```
        pbcopy < ~/.ssh/github.pub
        ```

        Register public key on GitHub

        `https://github.com/settings/keys`

    3. Edit ~/.ssh/config

        ```
        vim ~/.ssh/config
        ```
        ```
        Host *
            StrictHostKeyChecking no
            UserKnownHostsFile=/dev/null
            ServerAliveInterval 15
            ServerAliveCountMax 30
            AddKeysToAgent yes
            UseKeychain yes
            IdentitiesOnly yes

        Host github.com
            HostName github.com
            IdentityFile ~/.ssh/github
            User git

        Host github-granoeste.develop
            HostName github.com
            User git
            IdentityFile ~/.ssh/github-granoeste.develop
            UseKeychain yes
            AddKeysToAgent yes

        Host github-granoeste
            HostName github.com
            User git
            IdentityFile ~/.ssh/github-granoeste
            UseKeychain yes
            AddKeysToAgent yes
        ```

    4. Check
        ```
        ssh -T github.com
        ```
        ```
        Hi foobar! You've successfully authenticated, but GitHub does not provide shell access.
        ```

    [Mac GitHub SSH接続設定 \- Qiita](https://qiita.com/ucan-lab/items/e02f2d3a35f266631f24)

13. Node.js

    1. nodebrew

        Install
        ```
        brew install nodebrew
        ```

        Check
        ```
        nodebrew -v
        ```

        Post Proccess
        ```
        /usr/local/opt/nodebrew/bin/nodebrew setup_dirs
        ```

        Add Path
        ```
        echo 'export PATH=$PATH:$HOME/.nodebrew/current/bin' >> ~/.bash_profile
        ```

    2. Node.js

        Install
        ```
        nodebrew install-binary latest
        ```

        Check installed versions
        ```
        nodebrew list
        ```

        Enable version  (*Option)
        ```
        nodebrew use v10.10.0
        ```

        Check node and npm
        ```
        node -v
        ```
        ```
        npm -v 
        ```

    3. nodenv  (*Option)

        Install
        ```
        brew install nodenv
        nodenv init
        ```

        Initialize nodenv using bash_profile
        ```
        echo 'eval "$(nodenv init -)"' >> ~/.bash_profile
        ```

        Install 8.11.4 and use it
        ```
        nodenv install 8.11.4

        nodenv local 8.11.4

        $ nodenv versions
        system
        * 8.11.4 (set by /Users/USERS/.nodenv/.node-version)
        ```

        ref. [nodenv/nodenv: Manage multiple NodeJS versions\.](https://github.com/nodenv/nodenv#homebrew-on-mac-os-x)

14. Python

    1. python 

        Check
        ```
        python --version
        ```
        > Python 2.7.15
  
    2. pip 

        Check
        ```
        pip --version
        ```
        > pip 18.0 from /usr/local/lib/python2.7/site-packages/pip (python 2.7)
        

    3. PyEnv (*Option)

        Install
        ```
        brew install pyenv
        ```

        Check
        ```
        pyenv --version
        ```
        > pyenv 1.2.7
        
        Initialize pyenv using bash_profile
        ```
        echo -e 'if command -v pyenv 1>/dev/null 2>&1; then\n  eval "$(pyenv init -)"\nfi\nexport PATH="~/.pyenv/bin:$PATH"' >> ~/.bash_profile
        ```

        Install Python 2.7.15 and use it
        ```
        $ pyenv install 2.7.15
        python-build: use openssl from homebrew
        python-build: use readline from homebrew
        Downloading Python-2.7.15.tar.xz...
        -> https://www.python.org/ftp/python/2.7.15/Python-2.7.15.tar.xz
        Installing Python-2.7.15...
        python-build: use readline from homebrew
        Installed Python-2.7.15 to /Users/USER/.pyenv/versions/2.7.15

        $ pyenv versions
        * system (set by /Users/USER/.pyenv/version)
        2.7.15

        $ pyenv local 2.7.15

        $ pyenv versions
        system
        * 2.7.15 (set by /Users/USER/.python-version)

        $ python --version
        Python 2.7.15
        ```

        Update pip
        ```
        $ pip --version
        pip 9.0.3 from /Users/USER/.pyenv/versions/2.7.15/lib/python2.7/site-packages (python 2.7)

        $ pip install --upgrade pip

        $ pip --version
        pip 18.0 from /Users/USER/.pyenv/versions/2.7.15/lib/python2.7/site-packages/pip (python 2.7)
        ```

        Add Path
        ```
        $ echo 'export PATH=$PATH:$HOME/.local/bin' >> ~/.bash_profile
        ```

        ref. [pyenv/pyenv: Simple Python version management](https://github.com/pyenv/pyenv#installation)

15. AWS CLI & SAM

    Install
    ```
    $ pip install --user awscli
    $ pip install --user aws-sam-cli
    ```

    Verify your installation worked
    ```
    $ aws --version
    $ sam -–version
    ```

    ref. [awslabs/aws\-sam\-cli: AWS SAM CLI 🐿 is a CLI tool for local development and testing of Serverless applications](https://github.com/awslabs/aws-sam-cli)

16. ~~Docker Desktop~~

    [Install Docker for Mac \| Docker Documentation](https://docs.docker.com/docker-for-mac/install/)


    Verify
    ```
    docker --version
    Docker version 18.06.1-ce, build e68fc7a

    $ docker-compose --version
    docker-compose version 1.22.0, build f46880f

    $ docker-machine --version
    docker-machine version 0.15.0, build b48dc28d
    ```

    ref. [Docker for Macをインストールしてみた](https://qiita.com/scrummasudar/items/750aa52f4e0e747eed68)
    
    
16. Docker CLI

    Install docker and docker-machine
    ```
    brew install docker docker-machine
    ```
    
    Install VirtualBox
    ```
    brew install --cask virtualbox
    ```
    
    ref. [macOSでDocker Desktopをアンインストールしてdocker\-cli \+ docker\-machineで動かすようにする \- その手の平は尻もつかめるさ](https://moznion.hatenadiary.com/entry/2021/09/01/112601)
    

17. Firebase CLI

    Install
    ```
    npm install -g firebase-tools
    ```

    Check
    ```
    firebase --version
    ```

    ref. [Firebase CLI リファレンス  \|  Firebase](https://firebase.google.com/docs/cli/?hl=ja)


    Firebase Functions support Node.js 8 and 6 runtime only.

18. jq

    Install
    ```
    brew install jq
    ```

19. Vagrant

    VirtualBox Install
    ```
    brew cask install virtualbox
    ```

    vagrant
    ```
    brew cask install vagrant
    ```

20. Ruby with rbenv
    Install rbenv  
    ```
    brew install rbenv ruby-build rbenv-communal-gems
    ```

    Edit .bash_profile
    ```
    export RUBY_CONFIGURE_OPTS="--with-openssl-dir=$(brew --prefix openssl@1.1)"
    if which rbenv > /dev/null; then eval "$(rbenv init -)"; fi
    export PATH="$HOME/.rbenv/bin:$PATH"
    ```

    Check
    ```
    $ rbenv -v
    rbenv 1.1.2

    $ rbenv versions
    * system (set by /Users/katsumi.onishi/.rbenv/version)
    
    $ ruby -v
    ruby 2.3.7p456 (2018-03-28 revision 63024) [universal.x86_64-darwin18]
    ```

    Install 2.5.3
    ```
    $ rbenv install 2.5.3

    $ rbenv versions
    * system (set by /Users/katsumi.onishi/.rbenv/version)
    2.5.3
    ```

    Chenge version
    ```
    $ rbenv local 2.5.3

    $ rbenv versions
    system
    * 2.5.3 (set by /Users/katsumi.onishi/Develop/Biz/techcon_app/app/ios/.ruby-version)
    ```

    Install bundler
    ```
    $ rbenv exec gem install bundler
    
    $ rbenv exec bundle -v
    Bundler version 2.1.4
    ```

    Install Gems
    ```
    $ rbenv exec bundle install
    ```
    
    Install fastlane
    ```
    $ rbenv exec gem install fastlane

    $ rbenv exec  fastlane --version
    fastlane installation at path:
    /Users/katsumi.onishi/.rbenv/versions/2.5.3/lib/ruby/gems/2.5.0/gems/fastlane-2.141.0/bin/fastlane
    -----------------------------
    [✔] 🚀
    fastlane 2.141.0
    ```

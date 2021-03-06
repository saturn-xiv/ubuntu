FROM ubuntu:xenial

RUN echo '2018-12-17' >> /version

ENV DEBIAN_FRONTEND noninteractive

RUN apt-get update
RUN apt-get -y upgrade
RUN apt-get -y install zsh curl wget build-essential locales pkg-config dh-autoreconf \
  git vim nodejs python zsh sudo tzdata \
  gcc-arm-linux-gnueabihf g++-arm-linux-gnueabihf binutils-multiarch \
  python-pip openssh-client \
  libsodium-dev libsqlite3-dev libudev-dev libssl-dev libzmq3-dev liboping-dev

RUN dpkg --add-architecture armhf
RUN echo "deb [arch=amd64] http://archive.ubuntu.com/ubuntu/ xenial main restricted" > /etc/apt/sources.list
RUN echo "deb [arch=amd64] http://archive.ubuntu.com/ubuntu/ xenial-updates main restricted" >> /etc/apt/sources.list
RUN echo "deb [arch=amd64] http://archive.ubuntu.com/ubuntu/ xenial universe" >> /etc/apt/sources.list
RUN echo "deb [arch=amd64] http://archive.ubuntu.com/ubuntu/ xenial-updates universe" >> /etc/apt/sources.list
RUN echo "deb [arch=amd64] http://archive.ubuntu.com/ubuntu/ xenial multiverse" >> /etc/apt/sources.list
RUN echo "deb [arch=amd64] http://archive.ubuntu.com/ubuntu/ xenial-updates multiverse" >> /etc/apt/sources.list
RUN echo "deb [arch=amd64] http://archive.ubuntu.com/ubuntu/ xenial-backports main restricted universe multiverse" >> /etc/apt/sources.list
RUN echo "deb [arch=amd64] http://security.ubuntu.com/ubuntu/ xenial-security main restricted" >> /etc/apt/sources.list
RUN echo "deb [arch=amd64] http://security.ubuntu.com/ubuntu/ xenial-security universe" >> /etc/apt/sources.list
RUN echo "deb [arch=amd64] http://security.ubuntu.com/ubuntu/ xenial-security multiverse" >> /etc/apt/sources.list

RUN echo "deb [arch=armhf] http://ports.ubuntu.com/ xenial main restricted universe multiverse" >> /etc/apt/sources.list
RUN echo "deb [arch=armhf] http://ports.ubuntu.com/ xenial-security main restricted universe multiverse" >> /etc/apt/sources.list
RUN echo "deb [arch=armhf] http://ports.ubuntu.com/ xenial-updates main restricted universe multiverse" >> /etc/apt/sources.list
RUN echo "deb [arch=armhf] http://ports.ubuntu.com/ xenial-backports main restricted universe multiverse" >> /etc/apt/sources.list

RUN apt-get update
RUN apt-get -y install libsodium-dev:armhf libsqlite3-dev:armhf libudev-dev:armhf libssl-dev:armhf libzmq3-dev:armhf liboping-dev:armhf
RUN apt-get -y autoremove
RUN apt-get -y clean

RUN echo "en_US.UTF-8 UTF-8" > /etc/locale.gen
RUN locale-gen
RUN update-locale LANG=en_US.UTF-8
RUN update-alternatives --set editor /usr/bin/vim.basic

# deploy
RUN useradd -m deploy -s /bin/zsh
RUN passwd -l deploy
RUN echo 'deploy ALL=(ALL) NOPASSWD:ALL' > /etc/sudoers.d/101-deploy
USER deploy

RUN sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)" || true

RUN pip install --upgrade --user pip
RUN echo 'export PATH=$HOME/.local/bin:$PATH' >> $HOME/.zshrc
RUN zsh -c "source $HOME/.zshrc \
  && pip install --user ansible"

# nodejs
RUN curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh | zsh
RUN zsh -c "source $HOME/.zshrc \
  && nvm install node"

# rust
RUN curl https://sh.rustup.rs -sSf | sh -s -- -y
RUN echo 'source $HOME/.cargo/env' >> $HOME/.zshrc
RUN zsh -c "source $HOME/.zshrc \
  && rustup default nightly \
  && rustup target add armv7-unknown-linux-gnueabihf"

VOLUME /workspace /home/deploy/.ssh
WORKDIR /workspace

CMD ["/bin/zsh", "-l"]

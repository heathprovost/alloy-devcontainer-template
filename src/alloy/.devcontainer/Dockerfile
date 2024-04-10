FROM amazonlinux:2023

# args
ARG USERNAME=alloy
ARG USER_UID=1000
ARG USER_GID=1000
ARG TERM=xterm
ARG ZSH_THEME=fwalch
ARG DOTNET_SDK_VERSION=8.0.203
ARG NVM_VERSION=0.39.7
ARG NODE_VERSION=lts/iron
ARG ZSH_IN_DOCKER_VERSION=1.1.5

# install system packages
RUN dnf -y install sudo git zsh gzip tar nano findutils hostname nc bind-utils \
    # java
    java-1.8.0-amazon-corretto java-11-amazon-corretto-headless \
    # dotnet dependencies
    krb5-libs libicu openssl-libs zlib \
    # meteor dependencies
    cairo cairo-devel cairomm-devel libjpeg-turbo-devel pango pango-devel pangomm pangomm-devel giflib-devel \
    # cypress dependencies
    xorg-x11-server-Xvfb gtk3-devel nss alsa-lib \
    # install development tools
    && dnf -y groupinstall "Development Tools" \
    && dnf -y clean all

# install dotnet sdk
RUN bash -c "$(curl -L https://dot.net/v1/dotnet-install.sh)" @ --version $DOTNET_SDK_VERSION --install-dir /usr/share/dotnet \
    && printf '\nexport DOTNET_ROOT=/usr/share/dotnet\nexport DOTNET_CLI_TELEMETRY_OUTPUT=1\nexport PATH=$PATH:$DOTNET_ROOT:$DOTNET_ROOT/tools\n' > /etc/profile.d/dotnet.sh \
    && chmod +x /etc/profile.d/dotnet.sh

# install aws-cli
RUN curl https://awscli.amazonaws.com/awscli-exe-linux-$(uname -m).zip -o awscliv2.zip \
    && unzip -q awscliv2.zip \
    && ./aws/install \
    && rm awscliv2.zip \
    && rm -r aws/

# create non-root user
RUN groupadd --gid $USER_GID $USERNAME \
    && useradd --uid $USER_UID --gid $USER_GID -m $USERNAME \
    && echo $USERNAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME \
    && chmod 0440 /etc/sudoers.d/$USERNAME
# set user to non-root user for rest of installation
USER $USERNAME

# custom software installation
RUN echo "Running custom software installation" \
    # setup nvm - https://github.com/nvm-sh/nvm
    && bash -c "$(curl -L https://raw.githubusercontent.com/nvm-sh/nvm/v$NVM_VERSION/install.sh)" \
    && . ~/.nvm/nvm.sh \
    && source ~/.bashrc \
    # setup node
    && nvm install node $NODE_VERSION \
    # setup oh-my-zsh - https://github.com/deluan/zsh-in-docker
    && bash -c "$(curl -L https://github.com/deluan/zsh-in-docker/releases/download/v$ZSH_IN_DOCKER_VERSION/zsh-in-docker.sh)" -- -x \
        -t $ZSH_THEME \
        -p themes \
        -p git \
        -p nvm
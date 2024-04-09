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
RUN dnf -y install sudo git zsh gzip tar nano krb5-libs libicu openssl-libs zlib findutils \
    && dnf -y clean all

# create non-root user
RUN groupadd --gid $USER_GID $USERNAME \
    && useradd --uid $USER_UID --gid $USER_GID -m $USERNAME \
    && echo $USERNAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME \
    && chmod 0440 /etc/sudoers.d/$USERNAME

# install dotnet sdk
RUN bash -c "$(curl -L https://dot.net/v1/dotnet-install.sh)" @ --version $DOTNET_SDK_VERSION --install-dir /usr/share/dotnet

# set user to non-root user for rest of installation
USER $USERNAME

# custom software installation
RUN echo "Running custom software installation" \
    # setup nvm - https://github.com/nvm-sh/nvm
    && bash -c "$(curl -L https://raw.githubusercontent.com/nvm-sh/nvm/v$NVM_VERSION/install.sh)" \
    && . ~/.nvm/nvm.sh \
    && source ~/.bashrc \
    # dotnet sdk exports
    && printf '\n# dotnet exports\nexport DOTNET_ROOT=/usr/share/dotnet\nexport DOTNET_CLI_TELEMETRY_OUTPUT=1\nexport PATH=$PATH:$DOTNET_ROOT:$DOTNET_ROOT/tools\n' >> ~/.bashrc \
    && source ~/.bashrc \
    # setup node
    && nvm install node $NODE_VERSION \
    # setup oh-my-zsh - https://github.com/deluan/zsh-in-docker
    && bash -c "$(curl -L https://github.com/deluan/zsh-in-docker/releases/download/v$ZSH_IN_DOCKER_VERSION/zsh-in-docker.sh)" -- -x \
        -t $ZSH_THEME \
        -p themes \
        -p git \
        -p nvm
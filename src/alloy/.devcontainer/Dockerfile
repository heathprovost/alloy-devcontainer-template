FROM amazonlinux:2023

# args
ARG USERNAME=alloy
ARG USER_UID=1000
ARG USER_GID=1000
ARG TERM=xterm
ARG ZSH_THEME=fwalch
ARG DOTNET_SDK_PACKAGE=dotnet-sdk-8.0
ARG NVM_VERSION=0.39.7
ARG NODE_VERSION=lts/iron
ARG ZSH_IN_DOCKER_VERSION=1.1.5

# add microsoft rpm package repo and install packages
RUN rpm --import https://packages.microsoft.com/keys/microsoft.asc \
    && curl -o /etc/yum.repos.d/microsoft-prod.repo https://packages.microsoft.com/config/fedora/37/prod.repo \
    && dnf -y install sudo git zsh gzip tar nano findutils $DOTNET_SDK_PACKAGE \
    && dnf -y clean all \
    && dotnet workload update

# create non-root user
RUN groupadd --gid $USER_GID $USERNAME \
    && useradd --uid $USER_UID --gid $USER_GID -m $USERNAME \
    && echo $USERNAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME \
    && chmod 0440 /etc/sudoers.d/$USERNAME

# set user to non-root user for rest of installation
USER $USERNAME

# custom software installation
RUN echo "Running custom software installation" \
    # setup nvm and node - https://github.com/nvm-sh/nvm
    && bash -c "$(curl -L https://raw.githubusercontent.com/nvm-sh/nvm/v$NVM_VERSION/install.sh)" \
    && . ~/.nvm/nvm.sh \
    && source ~/.bashrc \
    && nvm install node $NODE_VERSION \
    # setup oh-my-zsh - https://github.com/deluan/zsh-in-docker
    && bash -c "$(curl -L https://github.com/deluan/zsh-in-docker/releases/download/v$ZSH_IN_DOCKER_VERSION/zsh-in-docker.sh)" -- -x \
        -t $ZSH_THEME \
        -p themes \
        -p git \
        -p nvm
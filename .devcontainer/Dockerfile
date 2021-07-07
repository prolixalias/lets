FROM ubuntu:focal

### base ###
ARG DEBIAN_FRONTEND=noninteractive

ENV LANG=en_US.UTF-8

RUN apt update && \
    apt upgrade -y && \
    apt install -y python pip curl wget git zip unzip bash-completion build-essential ninja-build htop jq less locales man-db nano software-properties-common sudo time vim multitail lsof ssl-cert fish zsh && \
    locale-gen en_US.UTF-8 && \
    wget https://apt.puppet.com/puppet-tools-release-focal.deb && \
    wget https://apt.puppetlabs.com/puppet6-release-focal.deb && \
    dpkg -i puppet6-release-focal.deb && \
    dpkg -i puppet-tools-release-focal.deb && \
    apt update && \
    apt install -y pdk puppet-agent puppet-bolt && \
    rm -f puppet6-release-bionic.deb puppet-tools-release-bionic.deb && \
    apt clean && \
    rm -rf /var/lib/apt/lists/*

### Gitpod user ###
# '-l': see https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#user
RUN useradd -l -u 33333 -G sudo -md /home/gitpod -s /bin/bash -p gitpod gitpod \
    # passwordless sudo for users in the 'sudo' group
    && sed -i.bkp -e 's/%sudo\s\+ALL=(ALL\(:ALL\)\?)\s\+ALL/%sudo ALL=NOPASSWD:ALL/g' /etc/sudoers

### Gitpod user (2) ###
USER gitpod

ENV HOME=/home/gitpod

WORKDIR $HOME

RUN sudo echo "Running 'sudo' for user: success" && \ 
    sudo usermod -s $(which zsh) gitpod && \
    sudo /opt/puppetlabs/puppet/bin/gem install homesick puppet-debugger hub -N && \
    sudo ln -s /opt/puppetlabs/puppet/bin/homesick /usr/local/bin/homesick && \
    /opt/puppetlabs/puppet/bin/homesick clone --force prolixalias/dotfiles && \
    /opt/puppetlabs/puppet/bin/homesick link --force && \
    pip install certbot-dns-route53

ENTRYPOINT /usr/bin/zsh
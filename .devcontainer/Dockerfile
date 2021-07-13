# base

FROM ubuntu:focal

ARG DEBIAN_FRONTEND=noninteractive

ENV LANG=en_US.UTF-8

# root user

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
    rm -rf /var/lib/apt/lists/* && \
    # '-l': see https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#user
    useradd -l -u 33333 -G sudo -md /home/localdev -s /bin/bash -p localdev localdev && \
    # passwordless 'sudo' group
    sed -i.bkp -e 's/%sudo\s\+ALL=(ALL\(:ALL\)\?)\s\+ALL/%sudo ALL=NOPASSWD:ALL/g' /etc/sudoers && \
    usermod -s $(which zsh) localdev && \
    /opt/puppetlabs/puppet/bin/gem install homesick puppet-debugger hub -N && \
    ln -s /opt/puppetlabs/puppet/bin/homesick /usr/local/bin/homesick

# localdev user

USER localdev

ENV HOME=/home/localdev

WORKDIR $HOME

# sudo echo "Running 'sudo' for localdev: success" && \
RUN echo 'sudo chown -R localdev:localdev ~/.aws ~/.ssh' > ~/.zshrc && \
    echo 'if [[ ! -a ~/.homesick/repos/dotfiles ]]; then' >> ~/.zshrc && \
    echo '  if [[ -x "$(command -v homesick)" ]]; then' >> ~/.zshrc && \
    echo '    mkdir -p ~/.homesick/repos/' >> ~/.zshrc && \
    echo '    cd ~/.homesick/repos' >> ~/.zshrc && \
    echo '    git -c http.sslVerify=false clone --recurse-submodules -j2 git@github.com:prolixalias/dotfiles.git' >> ~/.zshrc && \
    echo '    cd ~/.homesick/repos/dotfiles; git submodule update --init' >> ~/.zshrc && \
    echo '    [[ -a ~/.homesick/repos/dotfiles ]] && homesick link --force' >> ~/.zshrc && \
    echo '  fi' >> ~/.zshrc && \
    echo 'fi' >> ~/.zshrc && \
    echo 'source .zshrc' >> ~/.profile

COPY .zshrc /home/localdev/.zshrc

ENTRYPOINT /usr/bin/zsh

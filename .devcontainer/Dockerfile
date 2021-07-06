FROM ubuntu:focal

### base ###
ARG DEBIAN_FRONTEND=noninteractive

ENV LANG=en_US.UTF-8

RUN apt update && \
    apt upgrade -y && \
    apt install -y curl wget git zip unzip bash-completion build-essential ninja-build htop jq less locales man-db nano software-properties-common sudo time vim multitail lsof ssl-cert fish zsh && \
    locale-gen en_US.UTF-8 && \
    wget https://apt.puppet.com/puppet-tools-release-focal.deb && \
    wget https://apt.puppetlabs.com/puppet6-release-focal.deb && \
    dpkg -i puppet6-release-focal.deb && \
    dpkg -i puppet-tools-release-focal.deb && \
    apt update && \
    apt install -y pdk puppet-agent puppet-bolt && \
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

# use sudo so that user does not get sudo usage info on (the first) login
RUN sudo echo "Running 'sudo' for Gitpod: success" && \
    # create .bashrc.d folder and source it in the bashrc
    mkdir /home/gitpod/.bashrc.d && \
    (echo; echo "for i in \$(ls \$HOME/.bashrc.d/*); do source \$i; done"; echo) >> /home/gitpod/.bashrc

RUN sudo usermod -s $(which zsh) gitpod && \
    # sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)" && \
    # echo "plugins=(git gitignore github gem pip bundler python ruby docker docker-compose)" >> /home/gitpod/.zshrc && \
    # echo 'PATH="$PATH:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/opt/puppetlabs/bin:/opt/puppetlabs/puppet/bin"'  >> /home/gitpod/.zshrc && \
    sudo /opt/puppetlabs/puppet/bin/gem install homesick puppet-debugger hub -N && \
    sudo ln -s /opt/puppetlabs/puppet/bin/homesick /usr/local/bin/homesick && \
    mkdir -p /home/gitpod/.config/puppet && \
    /opt/puppetlabs/puppet/bin/ruby -r yaml -e "puts ({'disabled' => true}).to_yaml" > /home/gitpod/.config/puppet/analytics.yml && \
    rm -f puppet6-release-bionic.deb puppet-tools-release-bionic.deb && \
    /opt/puppetlabs/puppet/bin/homesick clone --force prolixalias/dotfiles && \
    /opt/puppetlabs/puppet/bin/homesick link --force
    # chown -R gitpod:gitpod /home/gitpod/.aws /home/gitpod/.ssh
ENTRYPOINT /usr/bin/zsh
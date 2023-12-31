---
author: Curious
title: "Note : Environment Setup"
date: 2023-08-17
math: true
slug: note-environment_setup
categories:
    - Note
tags:
    - Misc
---

## Git
```bash
git config --global user.name "Curious-Lucifer"
git config --global user.email curious.lucifer.domini@gmail.com
git config --global core.editor vim
git config --global init.defaultBranch master
```

---
## Pwn
```bash
sudo apt install -y gcc gdb git ruby-dev gcc-multilib g++-multilib vim-gtk3 make gawk bison libseccomp-dev tmux wget locales binutils nasm python3-pip libssl-dev
git clone https://github.com/pwndbg/pwndbg ~/pwndbg && cd ~/pwndbg && ./setup.sh
git clone https://github.com/scwuaptx/Pwngdb.git ~/Pwngdb && cat ~/Pwngdb/.gdbinit >> ~/.gdbinit && sed -i "s/source ~\/peda\/peda.py//g" ~/.gdbinit
sudo gem install seccomp-tools one_gadget
```

---
## Pwnbox

### Directory Structure
```
.
├── Dockerfile
├── data
│   └── 
├── script
│   ├── dbg
│   └── gdb_script
└── snippet
```

### Files
Dockerfile : 
```Dockerfile
FROM ubuntu:22.04

ENV DEBIAN_FRONTEND=noninteractive
ENV LC_ALL=en_US.UTF-8

RUN apt update && \
    apt upgrade -yq && \
    apt install -yq gcc gdb git ruby-dev gcc-multilib g++-multilib vim-gtk3 fish make gawk bison libseccomp-dev tmux wget locales binutils nasm python3-pip libssl-dev glibc-source && \
    locale-gen en_US.UTF-8

RUN pip3 install --upgrade pip
RUN pip3 install --upgrade pwntools

# compile glibc-2.35
RUN cd /usr/src/glibc && \
    tar xvf glibc-2.35.tar.xz && \
    mkdir glibc_dbg && \
    cd glibc_dbg && \
    ../glibc-2.35/configure --prefix $PWD --enable-debug && \
    make -j4

# install pwndbg
RUN git clone https://github.com/pwndbg/pwndbg ~/pwndbg && \
    cd ~/pwndbg && \
    ./setup.sh

# install pwngdb
RUN git clone https://github.com/scwuaptx/Pwngdb.git ~/Pwngdb && \
    cat ~/Pwngdb/.gdbinit >> ~/.gdbinit && \
    sed -i "s/source ~\/peda\/peda.py//g" ~/.gdbinit

RUN gem install seccomp-tools one_gadget
RUN echo "set-option -g default-shell /bin/fish" > /root/.tmux.conf

RUN mkdir /data /script
COPY ./script /script
RUN chmod +x /script/dbg
RUN ln -s /script/dbg /usr/bin/dbg

WORKDIR /data

ENV PWNBOX=True

CMD ["/bin/fish"]
```

dbg : 
```bash
#!/bin/bash

if [[ $# != 1 ]]; then
    echo "Usage : dbg <binary>";
    exit 0;
fi

exec gdb $1 -x /script/gdb_script
```

gdb_script : 
```
set exec-wrapper env LD_PRELOAD=/usr/src/glibc/glibc_dbg/libc.so
```

snippet : 
```bash
#!/bin/bash

VERSION="22.04";

if [[ $# == 0 ]]; then
    echo "========= VERSION : $VERSION =========";
    echo "Usage:";
    echo "Build environment:  ./snippet build";
    echo "Up pwnbox daemon:   ./snippet up";
    echo "Start pwnbox:       ./snippet start";
    echo "Get shell:          ./snippet shell";
    echo "Stop pwnbox daemon: ./snippet stop";
    exit 0
fi

if [[ $1 == "build" ]]; then
    docker build -t pwnbox:$VERSION .;
elif [[ $1 == "up" ]]; then
    docker run -it -d --name pwnbox_$VERSION -v `pwd`/data:/data pwnbox:$VERSION;
elif [[ $1 == "start" ]]; then
    docker start pwnbox_$VERSION;
elif [[ $1 == "shell" ]]; then
    docker exec -it pwnbox_$VERSION fish;
elif [[ $1 == "stop" ]]; then
    docker stop pwnbox_$VERSION;
fi
```

---
## LAMP

### Directory Structure
```
.
├── Dockerfile
├── db
├── docker-compose.yml
└── web
    └── 
```

### Files
Dockerfile :
```Dockerfile
FROM php:7.4.33-apache

RUN docker-php-ext-install mysqli && docker-php-ext-enable mysqli

RUN apt update && apt upgrade -yq
```

db : 
```bash
#!/bin/bash

docker exec -it mysql mysql -u root -p
```

docker-compose.yml : 
```yml
version: '2'

services:
    php_apache:
        container_name: php_apache
        build: .
        ports:
            - 8000:80
        volumes:
            - ./web:/var/www/html

    mysql:
        container_name: mysql
        image: mysql
        environment: 
            MYSQL_ROOT_PASSWORD: root
            MYSQL_DATABASE: TESTDB
            MYSQL_USER: curious
            MYSQL_PASSWORD: curious
```


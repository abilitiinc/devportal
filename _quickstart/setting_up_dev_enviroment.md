---
title: Dev enviroment instructions
position: 1
exclude: true
---
## Setting up dev env on Ubuntu 18.04.1 
For Ubuntu 18.04.1 users, after installing the right packages with `apt` AbilitiX
will build out of the box without further effort:
    # Required packages
    sudo apt install -y \
        autoconf \
        automake \
        cmake \
        gcc \
        g++ \
        git \
        libbz2-dev \
        libssl-dev \
        libtool \
        make \
        pkg-config \
        python3 \
        python3-jinja2 \
        zlib1g-dev \
        libsqlite3-dev
        
    # Boost packages (also required)
    sudo apt-get install -y \
        libboost-chrono-dev \
        libboost-context-dev \
        libboost-coroutine-dev \
        libboost-date-time-dev \
        libboost-filesystem-dev \
        libboost-iostreams-dev \
        libboost-locale-dev \
        libboost-program-options-dev \
        libboost-serialization-dev \
        libboost-signals-dev \
        libboost-system-dev \
        libboost-test-dev \
        libboost-thread-dev

    # Optional packages (not required, but will make a nicer experience)
    sudo apt install -y \
        doxygen \
        libncurses5-dev \
        libreadline-dev \
        perl
        
    git clone https://github.com/abilitiinc/AbilitiX
    cd AbilitiX
    git checkout master
    git submodule update --init --recursive
    
Now download CLion or other IDE for C/C++ development

https://download.jetbrains.com/cpp/CLion-2019.1.4.tar.gz



   

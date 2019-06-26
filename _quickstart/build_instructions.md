---
title: Build instructions
position: 3
exclude: true
---

### Compile-Time Options (cmake)

##### CMAKE_BUILD_TYPE=[Release/Debug]

Specifies whether to build with or without optimization and without or with
the symbol table for debugging. Unless you are specifically debugging or
running tests, it is recommended to build as release.

##### LOW_MEMORY_NODE=[OFF/ON]

Builds sophiatxd to be a consensus-only low memory node. Data and fields not
needed for consensus are not stored in the object database.  This option is
recommended for witnesses and seed-nodes.

##### CLEAR_VOTES=[ON/OFF]

Clears old votes from memory that are no longer required for consensus.

##### BUILD_SOPHIATX_TESTNET=[OFF/ON]

Builds sophiatx for use in a private testnet. Also required for building unit tests.

##### SKIP_BY_TX_ID=[OFF/ON]

By default this is off. Enabling will prevent the account history plugin querying transactions 
by id, but saving around 65% of CPU time when reindexing. Enabling this option is a
huge gain if you do not need this functionality.

## Building on Ubuntu 18.04.1 
For Ubuntu 18.04.1 users, after installing the right packages with `apt` SophiaTX
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
        
    git clone https://github.com/SophiaTX/SophiaTX
    cd SophiaTX
    git checkout master
    git submodule update --init --recursive
    mkdir build
    cd build
    cmake -DCMAKE_BUILD_TYPE=Release ..
    make -j$(nproc) sophiatxd
    make -j$(nproc) cli_wallet
    
    # optional
    make install  # defaults to /usr/local

## Building Boost 1.67

SophiaTX requires Boost 1.65 and works with versions up to 1.69 (including).
Here is how to build and install Boost 1.67 into your user's home directory

    export BOOST_ROOT=$HOME/opt/boost_1_67_0
    URL='http://sourceforge.net/projects/boost/files/boost/1.67.0/boost_1_67_0.tar.gz'
    wget "$URL"
    [ $( sha256sum boost_1_67_0.tar.gz | cut -d ' ' -f 1 ) == \
        "8aa4e330c870ef50a896634c931adf468b21f8a69b77007e45c444151229f665" ] \
        || ( echo 'Corrupt download' ; exit 1 )
    tar xzf boost_1_67_0.tar.gz
    cd boost_1_67_0
    ./bootstrap.sh "--prefix=$BOOST_ROOT"
    ./b2 -j$(nproc) install


## Building on Other Platforms

- Windows build instructions are available here https://github.com/SophiaTX/SophiaTX/wiki/Setting-up-Windows-build-enviroment

- The developers normally compile with gcc and clang. These compilers should
  be well-supported.
- Community members occasionally attempt to compile the code with mingw,
  Intel and Microsoft compilers. These compilers may work, but the
  developers do not use them. Pull requests fixing warnings / errors from
  these compilers are accepted.

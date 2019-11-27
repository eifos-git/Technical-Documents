# Setting up a Witness node.

This is an introduction for new Witnesses to get up to speed on the Peerplays blockchain. It is intended for Witnesses planning to join a live, already deployed, blockchain.

The following repository should be used in support of this document:

{% embed url="https://github.com/peerplays-network/peerplays" %}

## Building on Ubuntu 18.04 LTS and Installation Instructions

The following dependencies are necessary for a clean install of Ubuntu 18.04 LTS:

```text
sudo apt-get install gcc-5 g++-5 cmake make libbz2-dev\
    libdb++-dev libdb-dev libssl-dev openssl libreadline-dev\
     autoconf libtool git
```

### Build Boost 1.67.0

```text
mkdir $HOME/src
cd $HOME/src
export BOOST_ROOT=$HOME/src/boost_1_67_0
sudo apt-get update
sudo apt-get install -y autotools-dev build-essential  libbz2-dev libicu-dev python-dev
wget -c 'http://sourceforge.net/projects/boost/files/boost/1.67.0/boost_1_67_0.tar.bz2/download'\
     -O boost_1_67_0.tar.bz2
tar xjf boost_1_67_0.tar.bz2
cd boost_1_67_0/
./bootstrap.sh "--prefix=$BOOST_ROOT"
./b2 install
```

## Building Peerplays

```text
cd $HOME/src
export BOOST_ROOT=$HOME/src/boost_1_67_0
git clone https://github.com/peerplays-network/peerplays.git
cd peerplays
git submodule update --init --recursive
cmake -DBOOST_ROOT="$BOOST_ROOT" -DCMAKE_BUILD_TYPE=Release
make -j$(nproc)

make install # this can install the executable files under /usr/local
```

## Install Docker Image

```text
# Install docker
sudo apt install docker.io


# Add current user to docker group
sudo usermod -a -G docker $USER
# You need to restart your shell session, to apply group membership
# Type 'groups' to verify that you are a member of a docker group


# Build docker image (from the project root, must be a docker group member)
docker build -t peerplays .


# Start docker image
docker start peerplays

# Exposed ports
# # rpc service:
# EXPOSE 8090
# # p2p service:
# EXPOSE 1776
```

## Build Graphene

```text
cd ..
git clone https://github.com/cryptonomex/graphene.git
cd graphene
git submodule update --init --recursive
cmake -DBOOST_ROOT="$BOOST_ROOT" -DCMAKE_BUILD_TYPE=Debug .
make 
```

## Starting A Peerplays Witness Node

```text
git clone https://github.com/peerplays-network/peerplays.git
cd peerplays
git submodule update --init --recursive
cmake -DBOOST_ROOT="$BOOST_ROOT" -DCMAKE_BUILD_TYPE=Release .
make
./programs/witness_node/witness_node
```

Launching the Witness creates the required directories. 

{% hint style="danger" %}
Next stop the Witness node before continuing.
{% endhint %}

```text
$ vi witness_node_data_dir/config.ini
p2p-endpoint = 0.0.0.0:9777
rpc-endpoint = 127.0.0.1:8090
seed-node = 213.184.225.234:59500
```

{% hint style="success" %}
Start the Witness node back up.
{% endhint %}

```text
./programs/witness_node/witness_node
```

### Upgrading A Peerplays Witness Node

To minimize downtime of your Peerplays Witness node when upgrading, it's recommended to create a backup Witness server.

{% page-ref page="creating-a-backup-server.md" %}

## CLI Wallet Setup

The next step is to set up the CLI Wallet.

{% page-ref page="cli-wallet-setup.md" %}

## Auto-Starting the Witness Node

It's important for your Witness node to start when your system boots up. The `filepaths` here assume that you installed your witness into `/home/ubuntu/peerplays`

Step 1. Create a log file to hold your `stdout/err` logging

```text
sudo touch /var/log/peerplays.log
```

Step 2. Save this file in your Peerplays directory. `vi /home/ubuntu/peerplays/start.sh`

```text
#!/bin/bash

cd /home/ubuntu/peerplays
./programs/witness_node/witness_node &> /var/log/peerplays.log
```

Step 3. Make it executable

```text
chmod 744 /home/ubuntu/peerplays/start.sh
```

Step 4. Create this file: `sudo vi /etc/systemd/system/peerplays.service` 

{% hint style="warning" %}
Note the path for `start.sh`, if necessary, change it to match where your `start.sh` file actually is.
{% endhint %}

```text
[Unit]
Description=Peerplays Witness
After=network.target

[Service]
ExecStart=/home/ubuntu/peerplays/start.sh

[Install]
WantedBy = multi-user.target
```

Enable the service

```text
sudo systemctl enable peerplays.service
```

Make sure you don't get any errors

```text
sudo systemctl status peerplays.service
```

Stop your witness if it is currently running from previous steps, then start it with the service.

```text
sudo systemctl start peerplays.service
```

Check your logfile for entries

```text
tail -f /var/log/peerplays.log
```

### 

## BOS and MINT Setup

{% page-ref page="bos-and-mint-setup.md" %}



### 

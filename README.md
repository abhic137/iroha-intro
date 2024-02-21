# iroha-intro
Hyperledger Iroha getting started with. you will find instructions to run on ubuntu pc as well as on ubuntu pc 

## Creating a Docker Network

To operate, Iroha requires a PostgreSQL database or RocksDB. If you prefer PostgreSQL, you will need a container running it alongside Iroha.

Let’s start with creating a Docker network, so containers for Postgres and Iroha can run on the same virtual network and successfully communicate. In this guide we will call it iroha-network, but you can use any name. In your terminal write following command:
Same for both ubuntu and Raspberry pi (ARM64)
```sudo docker network create iroha-network```

## Starting PostgreSQL Container

Note

You do not need to start this container if your plan is to use Iroha with RocksDB and not PostgreSQL. Just skip to the next step.

Now we need to run PostgreSQL in a container, attach it to the network you have created before, and expose ports for communication:
For Ubuntu
```
sudo docker run --name some-postgres \
-e POSTGRES_USER=postgres \
-e POSTGRES_PASSWORD=mysecretpassword \
-p 5432:5432 \
--network=iroha-network \
-d postgres:9.5 \
-c 'max_prepared_transactions=100'

```
For Raspberrypi 4
```
sudo docker pull postgres:9.5
```
```
sudo docker run --name some-postgres \
-e POSTGRES_USER=postgres \
-e POSTGRES_PASSWORD=mysecretpassword \
-p 5432:5432 \
--network=iroha-network \
-d postgres:9.5 \
-c 'max_prepared_transactions=100'
```

## Creating Blockstore

Before we run Iroha container, we may create a persistent volume to store files, storing blocks for the chain. It is done via the following command:
Same for both
```
docker volume create blockstore
```
## Preparing the configuration files¶

Note

To keep things simple, in this guide we will create a network containing only a single node. To understand how to run several peers, follow Deploy

Now we need to configure our Iroha network. This includes creating a configuration file, generating keypairs for a users, writing a list of peers and creating a genesis block.

Don’t be scared away — we have prepared an example configuration for this guide, so you can start testing Iroha node now. In order to get those files, you need to clone the Iroha repository from Github or copy them manually (cloning is faster, though).
```
git clone -b main https://github.com/hyperledger/iroha --depth=1
```
Hint

--depth=1 option allows us to download only the latest commit and save some time and bandwidth. If you want to get a full commit history, you can omit this option.

## Starting Iroha Container¶

We are almost ready to launch our Iroha container. You just need to know the path to configuration files (from the step above).

Let’s start Iroha node in Docker container with the following command:
For ubuntu
```
sudo docker run --name iroha \
-d \
-p 50051:50051 \
-v $(pwd)/iroha/example:/opt/iroha_data \
-v blockstore:/tmp/block_store \
--network=iroha-network \
-e KEY='node0' \
hyperledger/iroha:latest

```
For Raspberrypi (ARM64)
```
sudo docker run --name iroha \
-d \
-p 50051:50051 \
-v $(pwd)/iroha/example:/opt/iroha_data \
-v blockstore:/tmp/block_store \
--network=iroha-network \
-e KEY='node0' \
msalimbe1/iroha-aarch64:dev
```

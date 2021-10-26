# Peerplays QA environment

This set of docker images contains a self-contained, Peerplays QA environment. It features 16 Peerplays nodes(running 27 witnesses), 16 Bitcoin SONs, 16 Hive SONs, 1 Redis and 1 faucet node.

## Hardware Requirements

Here's a guideline for the hardware requirements for building the QA environment:

| Memory | Storage | OS           |
| ------ | ------- | ------------ |
| 32GB   | \~300GB | Ubuntu 18.04 |

Of course, the requirements will be highly dependent on what you're using the environment for. Intensive development of an enterprise-level application will need much more resources than simply exploring your own private environment.

## Software Requirements

Following are the software requirements:

| OS           | Docker            | git              |
| ------------ | ----------------- | ---------------- |
| Ubuntu 18.04 | 20.10.8 or higher | 2.33.0 or higher |

\* If any of the software mentioned above is not installed, please use the following pages to install them:

1. ubuntu: https://ubuntu.com/server/docs/installation
2. docker: https://docs.docker.com/engine/install/ubuntu/
3. git: https://git-scm.com/book/en/v2/Getting-Started-Installing-Git

## Getting the Code

Clone the peerplays-utils project in gitlab to get the latest setup scripts for Peerplays QA environment:

```
git clone https://gitlab.com/PBSA/tools-libs/peerplays-utils.git
cd peerplays-utils/peerplays-qa-environment/ 
```

## Setting up the QA Environment

The environment setup needs to be run in the following order:

1. BITCOIN
2. HIVE
3. PEERPLAYS
4. REDIS
5. FAUCET

Note: After running the Peerplays initialization script, wait for the next maintenance block before start using the environment for testing.

### Before starting initialization, make sure that all nodes are down.

```
sudo docker-compose down
```

### BITCOIN

The first step is to build the bitcoin container. Open a new terminal and run the following command:

```
sudo docker-compose up bitcoin-for-peerplays
```

This will take some time to complete. Once you see the logs on the terminal have stopped, open a new terminal and login to the newly built Bitcoin container with the following command:

```
sudo docker exec -it peerplaysqaenvironment_bitcoin-for-peerplays_1 /bin/bash
```

Once inside the container run init-network.sh script to setup the Bitcoin environment:

```
./init-network.sh
```

### HIVE

Now we need to build the HIVE container. Open a new terminal and run the following command:

```
sudo docker-compose up hive-for-peerplays
```

This will take some time to complete. Wait till the HIVE blockchain starts generating blocks(Generated block #1 with timestamp ....) and then open a new terminal and login to the newly built HIVE container with the following command:

```
sudo docker exec -it peerplaysqaenvironment_hive-for-peerplays_1 /bin/bash
```

Once inside the container run init-network.sh script to setup the HIVE environment:

```
./init-network.sh
```

### PEERPLAYS

Next, we need to build the Peerplays container. Open a new terminal and run the following command:

```
sudo docker-compose up peerplays01 peerplays02 peerplays03 peerplays04 peerplays05 peerplays06 peerplays07 peerplays08 peerplays09 peerplays10 peerplays11 peerplays12 peerplays13 peerplays14 peerplays15 peerplays16
```

This will take some time to complete. Wait till the HIVE blockchain starts generating blocks(Generated block...) and then open a new terminal and login to the newly built HIVE container with the following command:

```
sudo docker exec -it peerplaysqaenvironment_peerplays01_1 /bin/bash
```

Once inside the container run init-network.sh script to setup the HIVE environment:

```
./init-network.sh
```

Note: Wait for the next maintenance block before start using the environment

### Redis

Next, we build the Redis container. Open a new terminal and run the following command:

```
sudo docker-compose up redis-for-peerplays
```

Wait for the message "Ready to accept connections"

### Faucet

The final container which we need to build is faucet. Open a new terminal and run the following command:

```
sudo docker-compose up faucet-for-peerplays
```

Wait for the message "Running on http://10.11.12.50:5000/ (Press CTRL+C to quit)"

## Viewing logs of bitcoin/hive/peerplays nodes

In order to view the logs of the containers following commands can be used:

1. Bitcoin

```
sudo docker logs -f `sudo docker ps -a|grep bitcoin|awk '{print $18}'`
```

2\. Hive

```
sudo docker logs -f `sudo docker ps -a|grep hive|awk '{print $15}'`
```

3\. Peerplays

There are 16 peerplays nodes running and in this example, we are trying to see the logs of peerplays10 node.

```
sudo docker logs -f `sudo docker ps -a|grep peerplays10|awk '{print $17}'`
```

Note: In case you want to see the logs of peerplays01 node replace peerplays10 with peerplays01 in the command given above.

## Node properties

The IP address and the  configuration of the nodes in the environment are given below:&#x20;

```
Bitcoin        10.11.12.201
Hive           10.11.12.202

Faucet         10.11.12.50
Redis          10.11.12.51

Peerplays01    10.11.12.101    Witness 1.6.1,  1.6.12,    SON 1.33.0,   Stale production ON
Peerplays02    10.11.12.102    Witness 1.6.2,  1.6.13,    SON 1.33.1,   Stale production OFF
Peerplays03    10.11.12.103    Witness 1.6.3,  1.6.14,    SON 1.33.2,   Stale production OFF
Peerplays04    10.11.12.104    Witness 1.6.4,  1.6.15,    SON 1.33.3,   Stale production OFF
Peerplays05    10.11.12.105    Witness 1.6.5,  1.6.16,    SON 1.33.4,   Stale production OFF
Peerplays06    10.11.12.106    Witness 1.6.6,  1.6.17,    SON 1.33.5,   Stale production OFF
Peerplays07    10.11.12.107    Witness 1.6.7,  1.6.18,    SON 1.33.6,   Stale production OFF
Peerplays08    10.11.12.108    Witness 1.6.8,  1.6.19.    SON 1.33.7,   Stale production OFF
Peerplays09    10.11.12.109    Witness 1.6.9,  1.6.20,    SON 1.33.8,   Stale production OFF
Peerplays10    10.11.12.110    Witness 1.6.10, 1.6.21,    SON 1.33.9,   Stale production OFF
Peerplays11    10.11.12.111    Witness 1.6.11, 1.6.22,    SON 1.33.10,  Stale production OFF
Peerplays13    10.11.12.112    Witness NONE          ,    SON 1.33.11,  Stale production OFF
Peerplays13    10.11.12.113    Witness NONE          ,    SON 1.33.12,  Stale production OFF
Peerplays14    10.11.12.114    Witness NONE          ,    SON 1.33.13,  Stale production OFF
Peerplays15    10.11.12.115    Witness NONE          ,    SON 1.33.14,  Stale production OFF
Peerplays16    10.11.12.116    Witness NONE          ,    SON 1.33.15,  Stale production OFF
```

## Monitoring resource usage

To monitor computer resource usage by docker containers in real-time, use the following command:

```
sudo docker stats
```

The output will be similar to this:

```
CONTAINER ID   NAME                                               CPU %     MEM USAGE / LIMIT     MEM %     NET I/O           BLOCK I/O        PIDS
0e6e806bd8d7   peerplays-qa-environment_faucet-for-peerplays_1    0.01%     53.77MiB / 30.77GiB   0.17%     7.34kB / 4.73kB   0B / 2.66MB      17
1c793a7f4520   peerplays-qa-environment_redis-for-peerplays_1     0.18%     2.48MiB / 30.77GiB    0.01%     5.29kB / 670B     0B / 0B          5
61bdcacc4617   peerplays-qa-environment_peerplays01_1             2.06%     65.34MiB / 30.77GiB   0.21%     1.4MB / 743kB     0B / 0B          14
851c6f4c7a36   peerplays-qa-environment_peerplays02_1             1.25%     65.21MiB / 30.77GiB   0.21%     1.39MB / 724kB    0B / 0B          14
513fdd80faab   peerplays-qa-environment_peerplays03_1             1.87%     65.27MiB / 30.77GiB   0.21%     1.4MB / 730kB     0B / 0B          14
14cbff0ec769   peerplays-qa-environment_peerplays05_1             1.46%     65.2MiB / 30.77GiB    0.21%     1.4MB / 728kB     0B / 0B          14
6f153091d3a5   peerplays-qa-environment_peerplays07_1             1.35%     65.32MiB / 30.77GiB   0.21%     1.42MB / 721kB    0B / 0B          14
ea56bb7d139a   peerplays-qa-environment_peerplays15_1             1.26%     65.17MiB / 30.77GiB   0.21%     1.38MB / 695kB    0B / 0B          14
d472e4031736   peerplays-qa-environment_peerplays04_1             1.76%     65.21MiB / 30.77GiB   0.21%     1.4MB / 725kB     0B / 0B          14
8dec8c46b208   peerplays-qa-environment_peerplays16_1             1.26%     65.28MiB / 30.77GiB   0.21%     1.38MB / 683kB    0B / 0B          14
4c1a0b42551f   peerplays-qa-environment_peerplays06_1             2.24%     65.22MiB / 30.77GiB   0.21%     1.39MB / 732kB    0B / 0B          14
407fbfcc1572   peerplays-qa-environment_peerplays10_1             0.95%     65.21MiB / 30.77GiB   0.21%     1.39MB / 730kB    0B / 0B          14
fb79c628adf2   peerplays-qa-environment_peerplays09_1             1.57%     65.21MiB / 30.77GiB   0.21%     1.4MB / 751kB     0B / 0B          14
6489809208f0   peerplays-qa-environment_peerplays13_1             1.57%     65.16MiB / 30.77GiB   0.21%     1.38MB / 704kB    0B / 0B          14
1b4a22127d71   peerplays-qa-environment_peerplays12_1             1.50%     65.22MiB / 30.77GiB   0.21%     1.39MB / 683kB    0B / 0B          14
63782a95bdcd   peerplays-qa-environment_peerplays08_1             2.09%     65.23MiB / 30.77GiB   0.21%     1.4MB / 738kB     0B / 0B          14
cfa91a760895   peerplays-qa-environment_peerplays14_1             1.30%     65.21MiB / 30.77GiB   0.21%     1.39MB / 678kB    0B / 0B          14
99dc6c45e39a   peerplays-qa-environment_peerplays11_1             1.18%     65.23MiB / 30.77GiB   0.21%     1.38MB / 736kB    0B / 0B          14
4951a2727f8a   peerplays-qa-environment_peerplays-base_1          0.00%     1.441MiB / 30.77GiB   0.00%     10.3kB / 0B       0B / 4.1kB       1
0e0449865359   peerplays-qa-environment_hive-for-peerplays_1      7.24%     8.797MiB / 30.77GiB   0.03%     5.23MB / 15.8MB   3.6MB / 0B       46
ea086b2c9798   peerplays-qa-environment_bitcoin-for-peerplays_1   0.16%     40.5MiB / 30.77GiB    0.13%     54.4kB / 46.7kB   4.1kB / 49.2kB   29
5ebba3d28e21   peerplays-qa-environment_ubuntu-for-peerplays_1    0.00%     1.441MiB / 30.77GiB   0.00%     15kB / 0B         0B / 4.1kB       1
```

# Peerplays QA environment

This set of docker images contains self contained, ready to go, Peerplays QA environment. It features 22 witnesses, 16 Bitcoin SONs, 16 Hive SONs and faucet.

## Hardware Requirements

Here's a guideline for the hardware requirements for the running of the local QA environment:

| Memory | Storage | OS |
| :--- | :--- | :--- |
| 32GB | ~300GB | Ubuntu 18.04 |

Of course, the requirements will be highly dependent on what you're using the environment for. Intensive development of an enterprise-level application will need much more resources than simply exploring your own private environment.

## Building docker images

Building docker images will take some time, as the software will be built from scratch.

```text
docker-compose build
```

## Start containers

Creates new containers for all images and starts them.

```text
docker-compose up
```

You can also start containers one by one, or in groups.

### Bitcoin

```text
docker-compose up bitcoin-for-peerplays
```

### Hive

```text
docker-compose up hive-for-peerplays
```

### Peerplays

Start any set of containers you need. peerplays01 must be started, as this is the node that starts block production. For full set of witnesses/SONs, you need to start nodes peerplays01-peerplays11.

```text
docker-compose up peerplays01
docker-compose up peerplays01 peerplays02
docker-compose up peerplays05 peerplays10 peerplays15

# Minimal full featured network, 22 witnesses (11 active), 11 SONs (7 active)
docker-compose up peerplays01 peerplays02 peerplays03 peerplays04 peerplays05 peerplays06 peerplays07 peerplays08 peerplays09 peerplays10 peerplays11

# Full network, 22 witnesses (11 active), 16 SONs (7 active)
docker-compose up peerplays01 peerplays02 peerplays03 peerplays04 peerplays05 peerplays06 peerplays07 peerplays08 peerplays09 peerplays10 peerplays11 peerplays12 peerplays13 peerplays14 peerplays15 peerplays16
```

### Redis

```text
docker-compose up redis-for-peerplays
```

### Faucet

```text
docker-compose up faucet-for-peerplays
```

## Stop containers

Stops running all active containers and deletes them

```text
docker-compose down
```

## Connecting to the containers

First part of the container name might be different \(peerplays-qa-environment\)

### Bitcoin

```text
docker exec -it peerplays-qa-environment_bitcoin-for-peerplays_1 /bin/bash
```

### Hive

```text
docker exec -it peerplays-qa-environment_hive-for-peerplays_1 /bin/bash
```

### Peerplays

```text
docker exec -it peerplays-qa-environment_peerplays01_1 /bin/bash
docker exec -it peerplays-qa-environment_peerplays02_1 /bin/bash
docker exec -it peerplays-qa-environment_peerplays03_1 /bin/bash
etc...
```

### Redis

```text
docker exec -it peerplays-qa-environment_redis-for-peerplays_1 /bin/bash
```

### Faucet

```text
docker exec -it peerplays-qa-environment_ubuntu-for-peerplays_1 /bin/bash
```

## Initializing network

Wait for one initialization to finish, before starting the next one. Initialization should be done in the following order:

* Bitcoin
* Hive
* Peerplays
* Redis
* Faucet

Once the Peerplays network initialization is finished, wait for maintenance block.

### Before starting initialization, make sure that all nodes are down.

```text
docker-compose down
```

### Bitcoin

```text
docker-compose up bitcoin-for-peerplays
docker exec -it peerplays-qa-environment_bitcoin-for-peerplays_1 /bin/bash
./init-network.sh
```

### Hive

```text
docker-compose up hive-for-peerplays
# Wait for "Generated block..." messages
docker exec -it peerplays-qa-environment_hive-for-peerplays_1 /bin/bash
./init-network.sh
```

### Peerplays

```text
docker-compose up peerplays01 peerplays02 peerplays03 peerplays04 peerplays05 peerplays06 peerplays07 peerplays08 peerplays09 peerplays10 peerplays11 peerplays12 peerplays13 peerplays14 peerplays15 peerplays16
# Wait for "Generated block..." messages
# Wait for nodes to sync (no "Not producing block..." messages)
docker exec -it peerplays-qa-environment_peerplays01_1 /bin/bash
./init-network.sh
# Wait for maintenance block
```

### Redis

```text
docker-compose up redis-for-peerplays
# Wait for message "Ready to accept connections"
```

### Faucet

```text
docker-compose up faucet-for-peerplays
# Wait for message "Running on http://10.11.12.50:5000/ (Press CTRL+C to quit)"
```

## Node properties

```text
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

To monitor computer resource usage by docker containers in real time, use the following command:

```text
docker stats
```

The output will be similar to this:

```text
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


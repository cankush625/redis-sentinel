# Redis Sentinel Demo

We required Sentinel to achieve automatic failover incase the primary Redis instance fails.

## Steps to setup Sentinel

### Step 1:
We need to have a primary Redis instance and Redis replica instances running.

### Step 2:
Create a configuration file for Redis Sentinel.

```
$ touch sentinel1.conf
```

Paste below settings in this configuration file:

```
port 5000
sentinel monitor myprimary 127.0.0.1 6379 2
sentinel down-after-milliseconds myprimary 5000
sentinel failover-timeout myprimary 60000
sentinel auth-pass myprimary a_strong_password
```

### Step 3:
Make two more copies of this file - sentinel2.conf and sentinel3.conf and edit them so that
the PORT configuration is set to 5001 and 5002, respectively.

### Step 4:
Initialize three sentinels in different terminal tabs.

```
# Tab 1
$ redis-server ./sentinel1.conf --sentinel

# Tab 2
$ redis-server ./sentinel2.conf --sentinel

# Tab3
$ redis-server ./sentinel3.conf --sentinel
```

### Step 5:
Check if we can connect to Sentinel and verify if it is working as expected.

Connect to one of the Sentinel instances and run below commands:

Get information about the primary:

```
SENTINEL master myprimary
```

Get information about the replicas connected to the primary instance:

```
SENTINEL replicas myprimary
```

Get information about other Sentinel:

```
SENTINEL sentinels myprimary
```

Get IP address of the current primary:

```
SENTINEL get-master-addr-by-name myprimary
```

### Step 6:
Check if replica is getting promoted as primary if primary got failed.

After killing the primary Redis instance, we should be able to see the failover process is started
in the Sentinel logs.
Now, if we check the IP address of primary then we should be able to see that the replica has
been promoted to primary.

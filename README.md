nagios-checks
=============

Various nagios checks that we use at Wanelo.

check_joyent_zone_mem 
---------------------
This script will use the Joyent tool "jinf" to validate that free RAM on the zone is within specified percentage thresholds.

Usage: 
```
./check_joyent_zone_mem  [-w <warn_perc>] [-c <critical_perc>]
```

Example:
```
./check_joyent_zone_mem -w 75 -c 90 
RSS OK : my-host.prod 47% used (4334Mb free)|rss=47%;70;85
```

check_sidekiq_queue
-------------------
Peeks into the Sidekiq queue using redis-cli and validates the queue depth is within a given warning/critical range.

Usage: 
```
./check_sidekiq_queue [-h host] [-a password] ([-q queue] || [ -s retry|schedule ]) [-n namespace] [-d db] [-w warn_perc] [-c critical_perc]
```

Defaults: localhost, no password, default queue, no namespace, db=0, warning at 500, critical at 1000.

```
./check_sidekiq_queue -h 10.100.1.12 -q activity -w 200 -c 1000
SIDEKIQ OK : redis-host.prod 0 on activity|sidekiq_queue_activity=0;200;1000
```

By passing -q flag you will be getting a size of a regular sidekiq queue, while passing -s flag allows checking the size of
retry and schedule sidekiq system queues.

check_postgres_replication
--------------------------
Checks transaction log position on a master PostgreSQL host and a replica and warns if the replica
is behind by a certain amount of data.

```
Usage: ./check_postgres_replication [ options ]
   -h   --host       replica host (default 127.0.0.1)
   -m   --master     master fqdn or ip (required)
   -U   --user       database user (default postgres)
   -x   --units      units of measurement to display (KB or MB, default MB)
   -w   --warning    warning threshold in bytes (default 10MB)
   -c   --critical   critical threshold in bytes (default 15MB)
```

Note that `--units` is only used in the response. No math is done to translate `--warning` or `--critical`,
which should be set as bytes. Thus, a 20MB warning would be set as 20971520.

check_twemproxy
---------------
Nagios check that utilizes twemproxy status page, and returns OK/SUCCESS when all backend servers
in the sharded cluster are connected, or CRITICAL otherwise.

```
Usage: ./check_twemproxy [-h host] [-p port]
```

Dependencies: ruby with JSON parser installed.

Example:

```
check_twemproxy --host  192.168.10.100
TWEMPROXY CRITICAL : 192.168.10.100 error with redis cluster [twitter_feed] problem shards: shard003,shard006
```

```
check_twemproxy --host  192.168.10.100
TWEMPROXY OK
```

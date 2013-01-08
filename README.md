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
./check_sidekiq_queue [-h host] [-a password] [-q queue] [-n namespace] [-d db] [-w warn_perc] [-c critical_perc]
```

Defaults: localhost, no password, default queue, no namespace, db=0, warning at 500, critical at 1000.

```
./check_sidekiq_queue -h 10.100.1.12 -q activity -w 200 -c 1000
SIDEKIQ OK : redis-host.prod 0 on default|sidekiq_queue_activity =0;200;1000
```

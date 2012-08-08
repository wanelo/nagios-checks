nagios-checks
=============

Various nagios checks that we use at Wanelo.com

check_joyent_zone_mem 
---------------------
This script will use Joyent tool "jinf" to validate that free RAM on the zone is within specified percentage thresholds.

Usage: ./check_joyent_zone_mem  [-w <warn_perc>] [-c <critical_perc>]

Eg: ./check_joyent_zone_mem  -w 60 -c 80   # warning at 60% or higher used, critical at 80%

```
./check_joyent_zone_mem -w 75 -c 90 
RSS OK : my-host.prod 47% used (4334Mb free)|rss=47%;70;85
```

check_sidekiq_queue
-------------------
Peaks into Sidekiq queue using redis-cli and validates the queue depth is within a given warning/critical range.

Usage: ./check_sidekiq_queue [ -h <host> ] [ -q <default> ] [ <-n mq> ] [ -d <redis-db> ] [-w <warn_perc>] [-c <critical_perc>]

Eg: ./check_sidekiq_queue -w 500 -c 2000   # warning at 500 or higher used, critical at 2000 or higher

```
./check_sidekiq_queue -h 10.100.1.12 -q default -w 200 -c 1000
SIDEKIQ OK : redis-host.prod 0 on default|sidekiq_queue_default=0;200;1000
```

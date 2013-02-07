#!/bin/bash
# ========================================================================================
# HAProxy nagios check of current queue depth.
#
# 2013 Wanelo Inc, Apache License.
#
# Usage: ./check_haproxy_queue [-s stats_socket] [-b backend ]
#                              [-w <warn_cnt>] [-c <critical_cnt>]
#   -b   --backend          name of backend to watch (ie "app_servers")
#   -s   --stats_socket     location of haproxy stats socket (default /var/run/haproxy.sock)
#   -w   --warning          warning threshold (default 100)
#   -c   --critical         critical threshold (default 500)
# ========================================================================================

# Nagios return codes
STATE_OK=0
STATE_WARNING=1
STATE_CRITICAL=2
STATE_UNKNOWN=3

QUEUE=0
NODENAME=`cat /etc/nodename`
STATS_SOCKET=/var/run/haproxy.sock

# set thresholds in bytes
WARNING_THRESHOLD=100
CRITICAL_THRESHOLD=500

# Parse parameters
while [ $# -gt 0 ]; do
    case "$1" in
        -b | --backend)
                shift
                BACKEND=$1
                ;;
        -s | --stats_socket)
                shift
                STATS_SOCKET=$1
                ;;
        -w | --warning)
                shift
                WARNING_THRESHOLD=$1
                ;;
        -c | --critical)
                shift
                CRITICAL_THRESHOLD=$1
                ;;
        *)  echo "Unknown argument: $1"
            exit $STATE_UNKNOWN
            ;;
        esac
shift
done

function result {
  DESCRIPTION=$1
  STATUS=$2

  if [ -z "$MESSAGE" ]; then
    MESSAGE="current queue size is $QUEUE"
  fi

  echo "QUEUE $DESCRIPTION : ${NODENAME} $MESSAGE|queue=${QUEUE};${WARNING_THRESHOLD};${CRITICAL_THRESHOLD}"
  exit $STATUS
}

function exit_on_error {
  if [ $1 -ne 0 ]; then
    MESSAGE=$2
    result "CRITICAL" $STATE_CRITICAL
  fi
}

if [ -z "$BACKEND" ]; then
  MESSAGE="missing backend parameter"
  result "CRITICAL" $STATE_CRITICAL
fi

STATS=`echo "show stat -1 2 -1" | nc -U $STATS_SOCKET | grep $BACKEND`
exit_on_error $? "error checking stats socket"
QUEUE=`echo $STATS | cut -d',' -f3`

# Output response
if [ $QUEUE -ge $WARNING_THRESHOLD ] && [ $QUEUE -lt $CRITICAL_THRESHOLD ]; then
  result "WARNING" $STATE_WARNING
elif [ $QUEUE -ge $CRITICAL_THRESHOLD ]; then
  result "CRITICAL" $STATE_CRITICAL
else
  result "OK" $STATE_OK
fi

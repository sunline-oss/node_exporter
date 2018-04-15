# Percona Node Exporter [![Build Status](https://travis-ci.org/sunline-oss/node_exporter.svg?branch=master)](https://travis-ci.org/sunline-oss/node_exporter)
  
Prometheus exporter for hardware and OS metrics exposed by \*NIX kernels, written
in Go with pluggable metric collectors.

The [WMI exporter](https://github.com/martinlindhe/wmi_exporter) is recommended for Windows users.

This fork adds HTTP Basic authentication and TLS support using [Percona's shared code for exporters](https://github.com/percona/exporter_shared).


## Collectors

There is varying support for collectors on each operating system. The tables
below list all existing collectors and the supported systems.

Which collectors are used is controlled by the `--collectors.enabled` flag.

### Enabled by default

Name     | Description | OS
---------|-------------|----
conntrack | Shows conntrack statistics (does nothing if no `/proc/sys/net/netfilter/` present). | Linux
cpu | Exposes CPU statistics | Darwin, Dragonfly, FreeBSD
diskstats | Exposes disk I/O statistics from `/proc/diskstats`. | Linux
edac | Exposes error detection and correction statistics. | Linux
entropy | Exposes available entropy. | Linux
exec | Exposes execution statistics. | Dragonfly, FreeBSD
filefd | Exposes file descriptor statistics from `/proc/sys/fs/file-nr`. | Linux
filesystem | Exposes filesystem statistics, such as disk space used. | Darwin, Dragonfly, FreeBSD, Linux, OpenBSD
hwmon | Expose hardware monitoring and sensor data from `/sys/class/hwmon/`. | Linux
infiniband | Exposes network statistics specific to InfiniBand configurations. | Linux
loadavg | Exposes load average. | Darwin, Dragonfly, FreeBSD, Linux, NetBSD, OpenBSD, Solaris
mdadm | Exposes statistics about devices in `/proc/mdstat` (does nothing if no `/proc/mdstat` present). | Linux
meminfo | Exposes memory statistics. | Darwin, Dragonfly, FreeBSD, Linux
netdev | Exposes network interface statistics such as bytes transferred. | Darwin, Dragonfly, FreeBSD, Linux, OpenBSD
netstat | Exposes network statistics from `/proc/net/netstat`. This is the same information as `netstat -s`. | Linux
sockstat | Exposes various statistics from `/proc/net/sockstat`. | Linux
stat | Exposes various statistics from `/proc/stat`. This includes CPU usage, boot time, forks and interrupts. | Linux
textfile | Exposes statistics read from local disk. The `--collector.textfile.directory` flag must be set. | _any_
time | Exposes the current system time. | _any_
uname | Exposes system information as provided by the uname system call. | Linux
vmstat | Exposes statistics from `/proc/vmstat`. | Linux
wifi | Exposes WiFi device and station statistics. | Linux
zfs | Exposes [ZFS](http://open-zfs.org/) performance statistics. | [Linux](http://zfsonlinux.org/)

### Disabled by default

Name     | Description | OS
---------|-------------|----
bonding | Exposes the number of configured and active slaves of Linux bonding interfaces. | Linux
buddyinfo | Exposes statistics of memory fragments as reported by /proc/buddyinfo. | Linux
devstat | Exposes device statistics | Dragonfly, FreeBSD
drbd | Exposes Distributed Replicated Block Device statistics | Linux
interrupts | Exposes detailed interrupts statistics. | Linux, OpenBSD
ipvs | Exposes IPVS status from `/proc/net/ip_vs` and stats from `/proc/net/ip_vs_stats`. | Linux
ksmd | Exposes kernel and system statistics from `/sys/kernel/mm/ksm`. | Linux
logind | Exposes session counts from [logind](http://www.freedesktop.org/wiki/Software/systemd/logind/). | Linux
meminfo\_numa | Exposes memory statistics from `/proc/meminfo_numa`. | Linux
mountstats | Exposes filesystem statistics from `/proc/self/mountstats`. Exposes detailed NFS client statistics. | Linux
nfs | Exposes NFS client statistics from `/proc/net/rpc/nfs`. This is the same information as `nfsstat -c`. | Linux
runit | Exposes service status from [runit](http://smarden.org/runit/). | _any_
supervisord | Exposes service status from [supervisord](http://supervisord.org/). | _any_
systemd | Exposes service and system status from [systemd](http://www.freedesktop.org/wiki/Software/systemd/). | Linux
tcpstat | Exposes TCP connection status information from `/proc/net/tcp` and `/proc/net/tcp6`. (Warning: the current version has potential performance issues in high load situations.) | Linux

### Deprecated

*These collectors will be (re)moved in the future.*

Name     | Description | OS
---------|-------------|----
gmond | Exposes statistics from Ganglia. | _any_
megacli | Exposes RAID statistics from MegaCLI. | Linux
ntp | Exposes time drift from an NTP server. | _any_

### Textfile Collector

The textfile collector is similar to the [Pushgateway](https://github.com/prometheus/pushgateway),
in that it allows exporting of statistics from batch jobs. It can also be used
to export static metrics, such as what role a machine has. The Pushgateway
should be used for service-level metrics. The textfile module is for metrics
that are tied to a machine.

To use it, set the `--collector.textfile.directory` flag on the Node exporter. The
collector will parse all files in that directory matching the glob `*.prom`
using the [text
format](http://prometheus.io/docs/instrumenting/exposition_formats/).

To atomically push completion time for a cron job:
```
echo my_batch_job_completion_time $(date +%s) > /path/to/directory/my_batch_job.prom.$$
mv /path/to/directory/my_batch_job.prom.$$ /path/to/directory/my_batch_job.prom
```

To statically set roles for a machine using labels:
```
echo 'role{role="application_server"} 1' > /path/to/directory/role.prom.$$
mv /path/to/directory/role.prom.$$ /path/to/directory/role.prom
```

## Building and running

    make
    ./node_exporter <flags>

## Running tests

    make test

## Visualize

There is a Grafana dashboard for Host available as a part of [PMM](https://www.percona.com/doc/percona-monitoring-and-management/index.html) project, you can see the demo [here](https://pmmdemo.percona.com/graph/dashboard/db/system-overview).

## Submit Bug Report

If you find a bug in Percona Node Exporter or one of the related projects, you should submit a report to that project's [JIRA](https://jira.percona.com) issue tracker.

Your first step should be [to search](https://jira.percona.com/issues/?jql=project=PMM%20AND%20component=Node_Exporter) the existing set of open tickets for a similar report. If you find that someone else has already reported your problem, then you can upvote that report to increase its visibility.

If there is no existing report, submit a report following these steps:

1. [Sign in to Percona JIRA.](https://jira.percona.com/login.jsp) You will need to create an account if you do not have one.
2. [Go to the Create Issue screen and select the relevant project.](https://jira.percona.com/secure/CreateIssueDetails!init.jspa?pid=11600&issuetype=1&priority=3&components=11710)
3. Fill in the fields of Summary, Description, Steps To Reproduce, and Affects Version to the best you can. If the bug corresponds to a crash, attach the stack trace from the logs.

An excellent resource is [Elika Etemad's article on filing good bug reports.](http://fantasai.inkedblade.net/style/talks/filing-good-bugs/).

As a general rule of thumb, please try to create bug reports that are:

- *Reproducible.* Include steps to reproduce the problem.
- *Specific.* Include as much detail as possible: which version, what environment, etc.
- *Unique.* Do not duplicate existing tickets.
- *Scoped to a Single Bug.* One bug per report.

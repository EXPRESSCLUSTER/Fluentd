# Fluentd
- Fluentd is an incredible log collecting tool.

## Fluentd with EXPRESSCLUSTER
- We are thinking which pattern is most suitable with EXPRESSCLUSTER. There are td-agent.conf file samples.

### Tail log file
- Reciever
  ```
  <source>
    @type forward
    port 24224
    bind 0.0.0.0
  </source>
  <match **>
    @type file
    format single_value
    append true
    path /var/log/td-agent/recv.log
    time_slice_format %Y-%m-%d
    <buffer>
      @type file
      path /var/log/td-agent/buf-recv
      flush_mode interval
      flush_interval 10s
    </buffer>
  </match>
  ```
- Sender
  ```
  <source>
    @type tail
    format none
    path /opt/nec/clusterpro/log/rc.log.cur
    tag td.clp
  </source>
  <match **>
    @type forward
    <server>
      host <IP address of receiver>
      port 24224
    </server>
  </match>

## Fluentd with EXPRSSCLUSTER X SingleServerSafe Container
- You can deploy EXPRESSCLUSTER X SingleServerSafe (SSS) on container to monitor MariaDB or PostgreSQL. For more detail, please refer to https://github.com/EXPRESSCLUSTER/kubernetes/blob/master/HowToDeploySSS.md.
- We are thinking how to deploy Fluentd container to send SSS logs to Fluentd.


# Fluentd with SingleServerSafe

## Index
- [Evaluation Environment](#evaluation-environment)
- [Install Fluentd on Container Host](#install-fluentd-on-container-host)
- [Deploy SingleServerSafe Container](#deploy-singleserversafe-container)
- [Deploy Fluentd DaemonSet](#deploy-fluentd-daemonSet)

## Evaluation Environment
- Fluentd runs on a container host to receive logs.
- Fluentd runs on a container as DaemonSet to tail container logs and send logs to Fluentd runs on the container host.
- MariaDB and EXPRESSCLUSTER X SingleServerSafe run on containers as StatefulSet.
  ```
  +-------------------------------------+
  | kubernetes v.1.20.2                 |
  | +----------------+                  |
  | | DaemonSet      |                  |
  | | +---------+  Send logs            |
  | | | Fluentd |-------------> Fluentd |
  | | |         |----------+            |
  | | +---------+    |     |            |
  | +----------------+     |            |
  |                        |            |
  | +----------------+     |            |
  | | StatefulSet    |     |            |
  | | +---------+    |     |            |
  | | | SSS     |    |     | Tail logs  |
  | | +---------+    |     |            |
  | |   | Monitoring |     |            |
  | | +-V-------+    |     |            |
  | | | MariaDB |    |     |            |
  | | +---------+    |     |            |
  | +----------------+     |            |
  |                        |            |
  | /var/log/containers <--+            |
  +-------------------------------------+
  ```

## Install Fluentd on Container Host 
1. Install Fluentd.
1. Edit /etc/td-agent/td-agent.conf as below.
   ```
   <source>
     @type forward
     port 24224
     bind 0.0.0.0
   </source>
   <match **>
     @type file
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
1. Start Fluentd.
   ```sh
   $ sudo systemctl daemon-reload
   $ sudo systemctl restart td-agent
   ```
## Deploy SingleServerSafe Container
1. Refer to the following documents.
   - [English](https://github.com/EXPRESSCLUSTER/kubernetes/blob/master/HowToDeploySSS.md)
   - [Japanese](https://github.com/EXPRESSCLUSTER/kubernetes/blob/master/HowToDeploySSS_jp.md)

## Deploy Fluentd DaemonSet
1. Download fluentd-rbac.yaml and deploy it.
   ```sh 
   $ kubectl apply -f fluentd-rbac.yaml
   ```
1. Download fluentd-daemonset-forward.yaml and deploy it.
   ```sh
   $ kubectl apply -f fluentd-daemonset-forward.yaml
   ```
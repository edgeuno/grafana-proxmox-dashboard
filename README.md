# grafana-proxmox-dashboard

A Graphana dashboard to visualize the status of different Proxmox clusters distributed in different data centers.

# Requirements

1. Proxmox Cluster o Server
2. A InfluxDB DataBase accesible from Internet or from you Proxmox Cluster
3. A Grafana Installation with access to you InfluDB DataBase

# Data configuration

Proxmox can send automatically metric to external server (https://pve.proxmox.com/wiki/External_Metric_Server). For EdgeUno we decide use InfluxDB for storage that metrics, after that we connect the db with Grafana a do the visualizations.

## InfluxDB

On InfluxDB we have to create a database for each Cluster, this is a UDP connetion and use a different por per database.

```

[[udp]]
   enabled = true
   bind-address = "0.0.0.0:8089"
   database = "proxmox-cluster-1"
   batch-size = 1000
   batch-timeout = "1s"

[[udp]]
   enabled = true
   bind-address = "0.0.0.0:8090"
   database = "proxmox-cluster-2"
   batch-size = 1000
   batch-timeout = "1s"

```

## Proxy server

Our InfluxDB server are in a private network (xx.xx.xx.xx) for that reason we need a proxy o port redirection that can manage the connetion to the database server, we are using a Proxy Server (xx.xx.xx.xx) this server have a ip public for server the connection to the remote locations.

This is the script for the udp redirection:

```
uredir 8089 xx.xx.xx.xx 8089
uredir 8090 xx.xx.xx.xx 8090

```

For this redirection i choose use a little package called uredir (https://github.com/troglobit/uredir) i use this package for install the service: https://launchpad.net/~mikelp/+archive/ubuntu/uredir

The next step are configure the Proxmox server.

## Proxmox server

On each location on the first node of the cluster  we create the file `/etc/pve/status.cfg` this files have the configuration needed for send the metrics to the database.


```
influxdb: proxmox-cluster-1
    server IP-OR-DNS-InfluDB-Server
    port 8091
```

Done, whit this you can send the proxmox metrics to InfluxDB, connect Grafana to that InfluxDB and create different kind of Dashboard visualizations for Proxmox.

# Grafana Dashboard

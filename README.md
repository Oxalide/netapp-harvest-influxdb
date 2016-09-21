# Send metrics from NetApp Harvest to InfluxDB and visualise them with Grafana

As a follow-up evolution of [this project](https://github.com/Oxalide/netapp-ocpm-influxdb) we wanted something more flexible, with better detail, and also able to collect statistics from 7-Mode NetApps(OCPM is compatible with cDOTs only).
Enter NetApp Harvest, a collection of Perl scripts provided by NetApp that collect a lot of statistics from 7-Modes, cDOTs and OCUMs, and send them to Graphite(and thanks to InfluxDB's "graphite compatibility mode", to InfluxDB as well).
NetApp harvest is extremely flexible, you can add stuff you want to get data on and you can also change the frequence(by default it runs every minute - much better than OCPM's unchangable 5min).

In order for InfluxDB to be able to parse all that data, it needs templates, and in that case, the templates are pretty complicated. 

# Prerequisites

* NetApp Harvest - you can download it from NetApp's website, along with a really nice and detailed installation guide
* InfluxDB 0.13+
* Grafana 3+

# Configuring InfluxDB

The file [influxdb.conf](./influxdb.conf) contains an example graphite config for InfluxDB, you can change it as you like, the most important part are the templates.
Copy the relevant parts to your own influxdb.conf (normally in /etc/influxdb/influxdb.conf) and restart the InfluxDB service.

# Configuring NetApp Harvest
If you have followed the NetApp Harvest Installation Guide, you now have a netapp-harvest.conf file with all of your NetApps and appropriate configuration.
To send the data to InfluxDB, you have to edit the Graphite part under [default] for it to look like this:

```

#====== Graphite server setup defaults ========================================
graphite_enabled  = 1
graphite_server   = IP_OR_FQDN_OF_YOUR_INFLUXDB_SERVER
graphite_port     = 2003
graphite_proto    = tcp
normalized_xfer   = mb_per_sec
normalized_time   = millisec
graphite_root     =  default
graphite_meta_metrics_root  = default

```

A few minutes later you should have plenty of data in your InfluxDB. You can check with the following:
```
$ # influx
Visit https://enterprise.influxdata.com to register for updates, InfluxDB server management, and monitoring.
Connected to http://localhost:8086 version 0.13.0
InfluxDB shell version: 0.13.0
> use graphite
Using database graphite
> show measurements

```

And there you should have some results.

# Getting started with Grafana
There's an example dashboard in [netapps_overview_harvest.json](./netapps_overview_harvest.json). Open it and change every occurence of "InfluxDB graphite" with your datasource name, and then import it to Grafana.


# License
This project is licensed under the MIT License, available in (LICENSE.txt](./LICENSE.txt)

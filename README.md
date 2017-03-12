# Memcached

[Memcached](http://memcached.org) is a Free & open source,
high-performance, distributed memory object caching system.

## Basic Configuration

| Config Key           | Description                               | Default                  |
|----------------------|-------------------------------------------|--------------------------|
| memcached.configfile | Path on filesystem for configuration file | /etc/sysconfig/memcached |
| memcached.port       | Port on which memcached should listen     | 11211                    |
| memcached.user       | User to run memcached                     | memcached                |
| memcached.maxconn    | Maximum Connections                       | 1024                     |
| memcached.cachesize  | Cache Size                                | 64                       |
| memcached.options    | Options to pass to memcached              |                          |



## Example Use

For a single node:

```

name: testApp
location: my-location
services:
  - type: 'memcached-node:0.1'
    id: memcached-node
    brooklyn.config:
      memcached.cachesize: 4096

```

As a cluster

```
name: testApp
location: my-location
services:
  - type: org.apache.brooklyn.entity.stock.BasicApplication
    id: app
    brooklyn.children:
    - type: brooklyn.entity.group.DynamicCluster
      id: memcached-cluster
      name: Memcached Cluster
      description: A cluster of Memcached nodes
      brooklyn.enrichers:
      - type: org.apache.brooklyn.enricher.stock.Aggregator
        brooklyn.config:
          enricher.sourceSensor: $brooklyn:sensor("host.address")
          enricher.targetSensor: $brooklyn:sensor("host.address.list")
          enricher.aggregating.fromMembers: true

      brooklyn.config:
        initialSize: 2
        memberSpec:
          $brooklyn:entitySpec:
            type: 'memcached-node:0.1'
            name: Memcached Node
            brooklyn.config:
              memcached.cachesize: 4096
```

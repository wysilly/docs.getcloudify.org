---
layout: bt_wiki
title: Manager Components Overview
category: Manager Architecture
draft: false
weight: 200

diamond_plugin_link: plugin-diamond.html
---

This topic provides an in-depth overview of the Cloudify architecture, including how it supports currently implemented, and potential, future flows. It assumes that you have an understanding of operational flows.


Cloudify Manager primarily consists of the following open-source components. The component's configuration, together with Cloudify code, is the core of Cloudify.

* [Nginx](#nginx)
* [Gunicorn](#gunicorn-and-flask)
* [Flask](#gunicorn-and-flask)
* [Elasticsearch](#elasticsearch)
* [Logstash](#logstash)
* [RabbitMQ](#rabbitmq)
* [Riemann](#riemann)
* [Celery](#celery)
* [InfluxDB](#influxdb-and-grafana)
* [Grafana](#influxdb-and-grafana)


![Cloudify components]({{< img "architecture/cloudify_advanced_architecture.png" >}})

## Ports and Entry Points

Rather than specifying the ports in each component's overview, ports are specified here so that you can easily review network requirements.

### External

By default, there are two external networks from which the Cloudify management environment is accessed:

* The network on which the CLI resides. This is likely to be your `management network`.
* The network on which the application resides. This is likely to be your appliication network.

Therefore, Cloudify aims to have only two entry points to its Management Environment:

* Ports 80/443 for user REST-service/UI access via NGINX
* Port 5672 for application access via RabbitMQ

* Port 53229 is currently exposed for fileserver access. (Will be done via ports 80 / 443 in a future version.)
* Port 22 is currently exposed for SSH access, so that the CLI can bootstrap the Cloudify management environment. While this is currently a requirement by our default bootstrap method in the CLI, using `userdata/cloudinit` to bootstrap will make this requirement obsolete as long as it's understood that `cfy ssh` will not work.
* Currently, the only "outside" access to the management environment that does not occur through one of these entry points is the Host Agent, which accesses Nginx directly (rather than through RabbitMQ) to update the application's model, for example when runtime-properties are set. 
<!--- This will be changed in future versions to reflect security requirements. --->

### Internal

Internally, the following ports are exposed:

* Port 8100 for the REST service
* Port 9100 for the UI 
* NGINX exposes port 8101 for internal REST access
* Elasticsearch exposes port 9200 port for the HTTP API
* RabbitMQ exposes 5672 
* InfluxDB exposes port 8086 port for the HTTP API
* Logstash exposes a dummy 9999 port for liveness verification

## Nginx

[NGINX](http://nginx.com/) is a high performaning Web server.

In Cloudify Manager, NGINX serves two purposes:

* A proxy for the Cloudify REST service and Web UI
* A fileserver to host Cloudify-specific resources, agent packages and bBlueprint resources

## Fileserver

The fileserver served by NGINX, while tied to NGINX by default, is not logically bound to it. That is, while we currently access it directly in several occurences (via disk rather than via network), we will be working towards having it completely decoupled from the management environment itself so that it can be deployed anywhere.

## Gunicorn and Flask

[Gunicorn](http://gunicorn.org/) is a WSGI HTTP server. [Flask](http://flask.pocoo.org/) is a web framework.

Gunicorn and Flask together serve as the Cloudify REST service. The REST service is written using Flask, and Gunicorn is the server. NGINX is the proxy to that server.
Essentially, the Cloudify REST service is what makes Cloudify function and is the integrator of all parts of the the system.

## Elasticsearch

[Elasticsearch](https://www.elastic.co/products/elasticsearch) is a JSON-based document store.

In Cloudify Manager, Elasticsearch serves two purposes:

* It is the main database that contains the application's model (i.e. Blueprints, deployments, runtime properties).
* It indexes and stores logs and events.

## Indices

Elasticsearch is initially provisioned with two indices:

* `cloudify_storage` - For storing the data model (Blueprints, deployments, runtime properties, etc..)
* `cloudify_events` - For storing logs and events 

The indices and their mappings are generated at build time. To keep the indices and their data persistent, they are mapped to a data container.

## Logstash

[Logstash](https://www.elastic.co/products/logstash) is a data handler. It can pull/push messages using several inputs, and apply filters and output to different outputs.

Logstash is used by Cloudify to pull log and event messages from RabbitMQ and index them in Elasticsearch.

# RabbitMQ

[RabbitMQ](http://www.rabbitmq.com/) is a queue-based messaging platform.

RabbitMQ is used by Cloudify as a message queue for the following purposes:

* Queueing deployment tasks
* Queueing logs and events
* Queueing metrics

<--- Currently not all requests between Cloudify's Manager and the hosts it manages go through RabbitMQ. We aim to make it so. --->

## Riemann

[Riemann](http://riemann.io/) is an event stream processor used mainly for monitoring.

Riemann is used within Cloudify as a policy based decision maker. For more information on policies, refer to the [policies]({{< relref "manager_policies/overview.md" >}}) section.

{{% gsNote title="Note" %}}
Riemann's use as a policy engine in Cloudify is an experimental feature and as such is not guaranteed to be forward compatible and is somewhat incomplete. 
{{% /gsNote %}}

## Celery

[Celery](http://www.celeryproject.org/) is a distributed task queue.

Cloudify's Management Worker, the Deployment Specific agents and the host agents are based on Celery.

### Deployment Specific Agents

Both the `deployment workflow agent` and the `deployment agent` drawn in the diagram are deployment specific. For every deployment created, two of these agents are spawned.

* The `deployment workflow agent` executes deployment specific workflows.
* The `deployment agent` executes API calls to IaaS providers to create deployment resources or submits tasks to RabbitMQ so that host agents can execute them.

Note that all agents (Management, Deployment Specific, Host) are actually the same physical entity (a virtualenv with Python modules - Cloudify plugins installed in them).

### Management Worker (or Agent)

An entity removed from the diagram is a management agent containing a Cloudify plugin able to spawn the aforementioned deployment specific agents. This agent is run during the bootstrap process.

## InfluxDB and Grafana

[InfluxDB](http://influxdb.com/) is a time series database. [Grafana](http://grafana.org/) is a graphical dashboard for InfluxDB.

* A proprietary metrics consumer is used to pull metrics from RabbitMQ and submit them to InfluxDB.
* InfluxDB is used by Cloudify to store metrics submitted (mainly) by the application's hosts.
* Grafana is embedded within Cloudify's Web UI to graph metrics stored within InfluxDB.

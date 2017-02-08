---
layout: bt_wiki
title: Overview
category: Manager
draft: false
weight: 1
---
{{% gsSummary %}}{{% /gsSummary %}}


Cloudify Manager comprises the following main parts:

* [Cloudify Manager](#the-manager)
* [Agents](#the-agents) (Unless you are running in agent-less mode)
* [Web UI](#the-web-ui) (only for Cloudify Premium customers)

## Cloudify Manager

Cloudify Manager consists of the Cloudify code and a set of Open-Source applications. A comprehensive explanation about these applications is provided [here]({{< relref "manager_architecture/components.md" >}}).

Cloudify Manager's architecture is designed to provide support for all potential operational flows you might require when managing your applications, such as:

* Event-stream processing
* Secured requests
* Metrics queueing, aggregation and analysis
* Log/event queueing, aggregation and analysis
* Manual or aAutomated task execution and queueing, based on live streams of events or aggregated data
* Interaction with Cloudify agents for executing tasks on application hosts, and maintaining them

You can also communicate with Cloudify Manager via the CLI, which uses the Cloudify REST client module to interact with the Cloudify REST service.

All requests are served via a proxy.

## Cloudify Agents

Cloudify agents are entities that are designed to execute tasks on application hosts. They can listen to task queues, and execute tasks when required.

Agents are designed to execute tasks using [Cloudify-specific plugins]({{< relref "plugins/overview.md" >}}).

In the background, the agents that are used on the hosts are also used in the Manager, but in a different context. For example, each deployment has two agents, one of which talks to IaaS APIs to deploy resources.

{{% gsNote title="Note" %}}
Cloudify can run in "agentless" mode, which means that agents can use certain plugins to manage hosts without the agents being installed on them. You can specify which server nodes have agents installed on them when creating the blueprint.
{{% /gsNote %}}

For more information on agents, click [here]({{< relref "agents/overview.md" >}}).

## Web Interface

Cloudify for Premium customers includes a Web interface. The Web interface provides many of the features of the CLI provides, and more. For now, The Cloudify Web interface is deployed alongside the Cloudify Manager. We're working on modularization and decoupling so that it can be deployed anywhere.

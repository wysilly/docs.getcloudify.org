---
layout: bt_wiki
title: Cloudify Manager
category: Intro
draft: false
weight: 500

---

Cloudify enables users to deploy applications using two main methods:

* Using the CLI only
* Using Cloudify Manager

Cloudify Manager is a dedicated environment, comprising an open-source stack that enables users to:

* Utilize its different plugins (e.g. Docker, Script, Chef and Puppet Plugins) to manage application hosts.
* Maintain a directory of their Blueprints.
* Create multiple deployments for each Blueprint and install them.
* Run hHealing, scaling and other custom workflows on their installed applications.
* Run multiple workflows concurrently.
* View metrics, search logs, and an application's topology
* Perform different tasks from the Cloudify Web interface.

Cloudify Manager also:

* Provides a secure environment for managing applications, via pluggable authentication and authorization mechanisms.
* Maintains a history of metrics and events.
* Manages agents running on an application's host machines.

While you can use Cloudify to provision resources directly from the CLI, Cloudify Manager should be used to manage production level applications.

To read more about Cloudify Manager, refer to the [Cloudify Manager]({{< relref "manager/getting-started.md" >}}) section.

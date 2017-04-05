---
layout: bt_wiki
title: nodes
category: Docs
draft: false
abstract: Cloudify's Command-Line Interface
weight: 140
---

The `cfy nodes` command is used to view information on the different nodes of a deployment.

You can use the command to list all nodes and get information on a single node.

#### Optional flags

These will work on each command:

* `-v, --verbose` - Show verbose output. You can supply this up to three times (i.e. -vvv)
* `-h, --help` - Show this message and exit.


## Commands


### list

#### Usage 
`cfy nodes list [OPTIONS] COMMAND [ARGS]`

Lists all nodes for a deployment.

If `DEPLOYMENT_ID` is provided, lists nodes for that deployment. Otherwise,
list nodes for all deployments.

#### Optional flags

* `-d, --deployment-id TEXT` - 
						The unique identifier for the deployment.
*  `--sort-by TEXT` - 	Key for sorting the list.
*  `--descending` - 	Sort list in descending order. [default: False]
*  `-t, --tenant-name TEXT` -  The name of the tenant from which to list the nodes. If unspecified, the current tenant is
                            used. This argument cannot be used simultaneously with the `all-tenants` argument.
*  `-a, --all-tenants` -    Include resources from all tenants associated with
                            the user. This argument cannot be used simultaneously with the `tenant-name` argument.  

&nbsp;
#### Example

```markdown
$ cfy nodes list
...

Listing all nodes...

Nodes:
+-----------------+------------------------------+------------------------------+---------+----------------------------------------------+---------------------+-----------------------------+------------+----------------+------------+
|        id       |        deployment\_id         |         blueprint\_id         | host\_id |                     type                     | number\_of_instances | planned\_number\_of\_instances | permission |  tenant\_name   | created\_by |
+-----------------+------------------------------+------------------------------+---------+----------------------------------------------+---------------------+-----------------------------+------------+----------------+------------+
| http\_web\_server | cloudify-hello-world-example | cloudify-hello-world-example |    vm   |           cloudify.nodes.WebServer           |          1          |              1              |  creator   | default\_tenant |   admin    |
|        vm       | cloudify-hello-world-example | cloudify-hello-world-example |    vm   |            cloudify.nodes.Compute            |          1          |              1              |  creator   | default\_tenant |   admin    |
|      mongod     | cloudify-nodecellar-example  | cloudify-nodecellar-example  |   host  |   nodecellar.nodes.MonitoredMongoDatabase    |          1          |              1              |  creator   | default\_tenant |   admin    |
|    nodecellar   | cloudify-nodecellar-example  | cloudify-nodecellar-example  |   host  | nodecellar.nodes.NodecellarApplicationModule |          1          |              1              |  creator   | default\_tenant |   admin    |
|       host      | cloudify-nodecellar-example  | cloudify-nodecellar-example  |   host  |       nodecellar.nodes.MonitoredServer       |          1          |              1              |  creator   | default\_tenant |   admin    |
|      nodejs     | cloudify-nodecellar-example  | cloudify-nodecellar-example  |   host  |        nodecellar.nodes.NodeJSServer         |          1          |              1              |  creator   | default\_tenant |   admin    |
+-----------------+------------------------------+------------------------------+---------+----------------------------------------------+---------------------+-----------------------------+------------+----------------+------------+

...

$ cfy nodes list -d simple_website
...

Listing nodes for deployment cloudify-hello-world-example...

Nodes:
+-----------------+------------------------------+------------------------------+---------+--------------------------+---------------------+-----------------------------+------------+----------------+------------+
|        id       |        deployment\_id         |         blueprint\_id         | host\_id |           type           | number\_of\_instances | planned\_number\_of\_instances | permission |  tenant\_name   | created\_by |
+-----------------+------------------------------+------------------------------+---------+--------------------------+---------------------+-----------------------------+------------+----------------+------------+
| http\_web\_server | cloudify-hello-world-example | cloudify-hello-world-example |    vm   | cloudify.nodes.WebServer |          1          |              1              |  creator   | default\_tenant |   admin    |
|        vm       | cloudify-hello-world-example | cloudify-hello-world-example |    vm   |  cloudify.nodes.Compute  |          1          |              1              |  creator   | default\_tenant |   admin    |
+-----------------+------------------------------+------------------------------+---------+--------------------------+---------------------+-----------------------------+------------+----------------+------------+

...
```


### get

#### Usage 
`cfy nodes get [OPTIONS] NODE_ID`

Retrieve information for a specific node of a specific deployment.

`NODE_ID` is the ID of the node for which to retrieve information.

#### Required flags

*  `-d, --deployment-id TEXT` - The unique identifier for the deployment. [required]

#### Optional flags
*  `-t, --tenant-name TEXT` -  The name of the tenant of the node. If unspecified, the current tenant is used
						

&nbsp;
#### Example

```markdown
$ cfy nodes get -d cloudify-nodecellar-example nodecellar
...

Retrieving node nodecellar for deployment cloudify-nodecellar-example

Node:
+------------+-----------------------------+-----------------------------+---------+----------------------------------------------+---------------------+-----------------------------+------------+----------------+------------+
|     id     |        deployment\_id        |         blueprint\_id        | host\_id |                     type                     | number\_of\_instances | planned\_number\_of\_instances | permission |  tenant\_name   | created\_by |
+------------+-----------------------------+-----------------------------+---------+----------------------------------------------+---------------------+-----------------------------+------------+----------------+------------+
| nodecellar | cloudify-nodecellar-example | cloudify-nodecellar-example |   host  | nodecellar.nodes.NodecellarApplicationModule |          1          |              1              |  creator   | default\_tenant |   admin    |
+------------+-----------------------------+-----------------------------+---------+----------------------------------------------+---------------------+-----------------------------+------------+----------------+------------+

Node properties:
	application_url: https://github.com/cloudify-cosmo/nodecellar/archive/master.tar.gz
	port: 8080
	startup_script: server.js

Node instance IDs:
	nodecellar_gj0mj2


...
```
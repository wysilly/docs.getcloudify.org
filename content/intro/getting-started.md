---
layout: bt_wiki
title: Tutorial: Installing a Sample Blueprint
category: Intro
draft: false
weight: 300

---
{{% gsSummary %}}{{% /gsSummary %}}


After [installing Cloudify]({{< relref "intro/installation.md" >}}), you can deploy your first application.

Cloudify Applications are defined in what we call `blueprints`, which are a logical representation of an application.
Blueprints can contain everything your application requires - from infrastructure elements, through configuration scripts to application elements, the way resources relate to one another, and much more.

In this tutorial, you will deploy a Blueprint to configure a Python web server locally.


## Installing the Sample Application

1 Download the already-prepared application from [here](https://github.com/cloudify-examples/simple-python-webserver-blueprint/archive/master.zip).

2 Extract the file to a directory and navigate to that directory.

3 Run the following to prepare the environment for the application:

```{{< gsHighlight  bash >}}
$ cfy local init --blueprint-path blueprint.yaml --inputs '{"webserver_port": "8000", "host_ip":"localhost"}'
...```

You have now initiated `blueprint.yaml`.
If you make changes to the blueprint, run `cfy local init -p blueprint.yaml`  to apply the changes.

```...
{{< /gsHighlight >}}```

You have now initialized the blueprint and provided it with some configuration, so it is ready to be deployed.

Cloudify provisions and manages applications using workflows. Workflows are predefined or user-defined flows that perform certain actions on your application.

The default workflow used by Cloudify to deploy an application is called the `install` workflow.

To deploy the application, run:

``{{< gsHighlight  bash >}}
$ cfy local execute --workflow install
...

2015-11-21 10:56:02 CFY <local> Starting 'install' workflow execution
2015-11-21 10:56:02 CFY <local> [host_cd5ef] Creating node
2015-11-21 10:56:02 CFY <local> [host_cd5ef] Configuring node
2015-11-21 10:56:02 CFY <local> [host_cd5ef] Starting node
2015-11-21 10:56:03 CFY <local> [http_web_server_9eaa3] Creating node
2015-11-21 10:56:03 CFY <local> [http_web_server_9eaa3.create] Sending task 'script_runner.tasks.run'
2015-11-21 10:56:03 CFY <local> [http_web_server_9eaa3.create] Task started 'script_runner.tasks.run'
2015-11-21 10:56:03 LOG <local> [http_web_server_9eaa3.create] INFO: Running WebServer locally on port: 8000
2015-11-21 10:56:03 LOG <local> [http_web_server_9eaa3.create] INFO: Setting `pid` runtime property: 27924
2015-11-21 10:56:03 CFY <local> [http_web_server_9eaa3.create] Task succeeded 'script_runner.tasks.run'
2015-11-21 10:56:04 CFY <local> [http_web_server_9eaa3] Configuring node
2015-11-21 10:56:04 CFY <local> [http_web_server_9eaa3] Starting node
2015-11-21 10:56:05 CFY <local> 'install' workflow execution succeeded

...
{{< /gsHighlight >}}```

Assuming that the `install` workflow has deployed successfully, when you go to `curl http://localhost:8000`, you will see that your application is up. If you are in a Windows environment, go to `http://localhost:8000` in your browser to see your output.

Cloudify is extensible and supports a variety of plugins. For example, when we installed our application, Cloudify used our pre-defined Script plugin to execute a number of scripts and deploy the Web server.

To deploy our application on a server in a cloud provider, for example AWS, we could have used the AWS and Fabric (SSH) plugins to achieve that.

In this tutorial, we are only using the Script plugin, which we already installed when we installed Cloudify. However, when a blueprint uses different plugins, they can be installed as follows:

```{{< gsHighlight  bash >}}
$ cfy local install-plugins -p BLUEPRINT_PATH
{{< /gsHighlight >}}```



## Retrieving Information about Your Deployed Application

Cloudify retains the entire structure of your application in a *model*.

Before a Blueprint is ready to be deployed, a *deployment* is created. A deployment is an instance of a Blueprint. The deployment is also a part of the model. The deployment model contains every piece of information that is contained in your application. For example, information set during run-time such as IP addresses, and predefined configuration properties such as application ports.

Now you will ouput some information about the application.

Run the following:

```{{< gsHighlight  bash >}}
$ cfy local outputs
...

{
  "http_endpoint": "http://localhost:8000"
}

...
{{< /gsHighlight >}}```

This printout shows the outputs from the Blueprint's deployment. In this instance, you can see that an output of the deployment is the endpoint of your server.


Each logical entity in your application that is defined within a Blueprint is a called a *node*. After a deployment is created, each logical node becomes a set of one or more *node instances*, which are instances of that node. A node can have multiple node instances, for example in the case of there being multiple virtual machines.

In this example are two nodes, each with a single instance. Let's list the instances:

```{{< gsHighlight  bash >}}
$ cfy local instances
...

[
  {
    "host_id": "host_cd5ef",
    "id": "http_web_server_9eaa3",
    "name": "http_web_server",
    "node_id": "http_web_server",
    "relationships": [
      {
        "target_id": "host_cd5ef",
        "target_name": "host",
        "type": "cloudify.relationships.contained_in"
      }
    ],
    "runtime_properties": {
      "pid": 27924
    },
    "state": "started",
    "version": 8
  },
  {
    "host_id": "host_cd5ef",
    "id": "host_cd5ef",
    "name": "host",
    "node_id": "host",
    "relationships": [],
    "runtime_properties": {},
    "state": "started",
    "version": 7
  }
]

...
{{< /gsHighlight >}}```

You can see all the available information on the two instances, such as their names and their *relationships* to other nodes.


## Uninstalling the Application

An `uninstall` workflow, which is also built in to Cloudify, anables you to uninstall a deployed Blueprint.

You will now uninstall your application.

{{< gsHighlight  bash >}}
$ cfy local execute -w uninstall
...

2015-11-21 11:07:51 CFY <local> Starting 'uninstall' workflow execution
2015-11-21 11:07:51 CFY <local> [http_web_server_9eaa3] Stopping node
2015-11-21 11:07:51 CFY <local> [http_web_server_9eaa3] Deleting node
2015-11-21 11:07:51 CFY <local> [http_web_server_9eaa3.delete] Sending task 'script_runner.tasks.run'
2015-11-21 11:07:51 CFY <local> [http_web_server_9eaa3.delete] Task started 'script_runner.tasks.run'
2015-11-21 11:07:51 LOG <local> [http_web_server_9eaa3.delete] INFO: Running process PID: 30367
2015-11-21 11:07:51 LOG <local> [http_web_server_9eaa3.delete] INFO: Python Webserver Terminated!
2015-11-21 11:07:51 CFY <local> [http_web_server_9eaa3.delete] Task succeeded 'script_runner.tasks.run'
2015-11-21 11:07:52 CFY <local> [host_cd5ef] Stopping node
2015-11-21 11:07:52 CFY <local> [host_cd5ef] Deleting node
2015-11-21 11:07:53 CFY <local> 'uninstall' workflow execution succeeded

...
{{< /gsHighlight >}}

We've now uninstalled our application. The model was erased and the Web Server is gone. Aww...

That's it! You've just deployed your first application using Cloudify.

You can now learn about the [blueprint]({{< relref "intro/blueprints.md" >}}) you just deployed.

---
layout: bt_wiki
title: Create a scaling Blueprint
category: Blueprints
draft: false
weight: 1300

---

Earlier we * [Initiated a VM on AWS](http://stage-docs.getcloudify.org/howto/user_guide/aws-vm/)

Now we would like to give that VM an Elastic IP, and demonstrate how those 2 scale together.

In order 

The ability we want to show here, is having several dependent objects scale together.
For our example, we will use an AWS instance and an Elastic IP.

We will start with our blueprint from the previous section: [Starting an Instance in AWS]({{< relref "user_guide/aws-vm.md" >}})

{{< gsHighlight  yaml >}}

tosca_definitions_version: cloudify_dsl_1_3

description: >
  This blueprint defines a EC2 VM created using Cloudify's AWS plugin
  and a simple web server started using Cloudify's script plugin.
  In addition, an elastic IP and a security group are created and associated with the created VM.
imports:
  - http://www.getcloudify.org/spec/cloudify/3.5m1/types.yaml
  - http://www.getcloudify.org/spec/aws-plugin/1.4.1/plugin.yaml

node_templates:
  elastic_ip:
    type: cloudify.aws.nodes.ElasticIP

  security_group:
    type: cloudify.aws.nodes.SecurityGroup
    properties:
      description: Security group for Hello World VM
      rules:
        - ip_protocol: tcp
          from_port: 22
          to_port: 22
          cidr_ip: 0.0.0.0/0
  vm:
    type: cloudify.aws.nodes.Instance
    properties:
      agent_config:
        user: 'centos'
      image_id: 'ami-dcdc95b6'
      instance_type: m3.medium
    relationships:
      - type: cloudify.aws.relationships.instance_connected_to_elastic_ip
        target: elastic_ip
      - type: cloudify.aws.relationships.instance_connected_to_security_group
        target: security_group
              
{{< /gsHighlight >}}

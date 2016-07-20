---
layout: bt_wiki
title: Create a scaling Blueprint
category: Blueprints
draft: false
weight: 1300

---

{{% gsNote title="Prerequisites addition" %}}
This requires an AWS based cloudify manager. 
[For instructions on how to start a cloudify manager on aws please see here](http://stage-docs.getcloudify.org/howto/manager/bootstrap-ref-aws.md/)
{{< /gsNote >}}

Earlier we * [Initiated a VM on AWS][Initiated a VM on AWS](http://stage-docs.getcloudify.org/howto/user_guide/aws-vm/)

We will start with our blueprint from the previous section: [Starting an Instance in AWS]({{< relref "user_guide/aws-vm.md" >}})

{{< gsHighlight  yaml >}}

tosca_definitions_version: cloudify_dsl_1_3

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
    type: cloudify.aws.nodes.Instance``
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

We can install this blueprint via: `cfy install -p blueprint.yaml -g`

{{< gsHighlight  markdown  >}}

Uploading blueprint blueprint.yaml to management server 54.221.249.43
 Blueprint uploaded. The blueprint's id is 3.5m1_F3FY7J
 Processing inputs source: inputs.yaml
 Creating new deployment from blueprint 3.5m1_F3FY7J at management server 54.221.249.43
 Deployment created. The deployment's id is 3.5m1_F3FY7J_3RDRW7
 Executing workflow 'install' on deployment '3.5m1_F3FY7J_3RDRW7' at management server 54.221.249.43 [timeout=900 seconds]
 Deployment environment creation is in progress...
 2016-07-20T21:30:49 CFY <3.5m1_F3FY7J_3RDRW7> Starting 'create_deployment_environment' workflow execution
 .
 .
 .
 2016-07-20T21:32:42 CFY <3.5m1_F3FY7J_3RDRW7> 'install' workflow execution succeeded
 Finished executing workflow install on deployment 3.5m1_F3FY7J_3RDRW7
 
{{< /gsHighlight >}}

This blueprint can allow us to scale individual elements like so:

`cfy executions start -w scale -p "{'scalable_entity_name':'vm'}" -d deployment_name`

however, when we do this, one of the VM's is not connected to an elastic IP, 
since a single IP cannot be connected to multiple instances.

Lets now alter our blueprint to have both Elastic IP and VM scale together.
Since our nodes are primitives 

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
    capabilities:
      scalable:```
        properties:
          default_instances: 1

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
    capabilities:
      scalable:
        properties:
          default_instances: 1

groups:
  vm_with_elastic_ip:
    members: [vm, elastic_ip]

policies:
  scale_policy2:
    type: cloudify.policies.scaling
    properties:
      default_instances: 2
    targets: [vm_with_elastic_ip]

{{< /gsHighlight >}}

`cfy executions start -w scale -p "{'scalable_entity_name':'vm_with_elastic_ip'}" -d deployment_name`

{{< gsHighlight  markdown  >}}

2016-07-20T21:36:26 CFY <3.5m1_F3FY7J_3RDRW7> [vm_8hlfei.start] Sending task 'cloudify_agent.installer.operations.start'
2016-07-20T21:36:26 CFY <3.5m1_F3FY7J_3RDRW7> [vm_8hlfei.start] Task started 'cloudify_agent.installer.operations.start'
2016-07-20T21:36:34 CFY <3.5m1_F3FY7J_3RDRW7> [vm_8hlfei.start] Task succeeded 'cloudify_agent.installer.operations.start'
2016-07-20T21:36:34 CFY <3.5m1_F3FY7J_3RDRW7> [vm_8hlfei->elastic_ip_y7wfop|establish] Sending task 'ec2.elasticip.associate'
2016-07-20T21:36:34 CFY <3.5m1_F3FY7J_3RDRW7> [vm_8hlfei->elastic_ip_y7wfop|establish] Task started 'ec2.elasticip.associate'
2016-07-20T21:36:35 CFY <3.5m1_F3FY7J_3RDRW7> [vm_8hlfei->elastic_ip_y7wfop|establish] Task succeeded 'ec2.elasticip.associate'
2016-07-20T21:36:36 CFY <3.5m1_F3FY7J_3RDRW7> 'scale' workflow execution succeeded
 
{{< /gsHighlight >}}

as you can see now, the vm and ip scaled together.
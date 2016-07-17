---
layout: bt_wiki
title: Create a scaling Blueprint
category: Blueprints
draft: false
weight: 1300

---

Earlier we * [Initiated a VM on AWS](http://stage-docs.getcloudify.org/howto/user_guide/aws-vm/)

Now we would like to give that VM an Elastic IP, and demonstrate how those 2 scale together.

The ability we want to show here, is having several dependent objects scale together.
For our example, we will use an AWS instance and an Elastic IP.

We will start with our blueprint from the previous section: [Starting an Instance in AWS]({{< relref "user_guide/aws-vm.md" >}})


{{< gsHighlight  yaml >}}

tosca_definitions_version: cloudify_dsl_1_3

description: >
  This blueprint uses the Cloudify AWS plugin to install a
  Cloudify intro Tutorial application. It uses AWS as the infrastructre

imports:
  - http://www.getcloudify.org/spec/cloudify/3.4/types.yaml
  - http://www.getcloudify.org/spec/aws-plugin/1.4.1/plugin.yaml

inputs:

  aws_access_key_id:
    type: string
    default: ''

  aws_secret_access_key:
    type: string
    default: ''

  aws_region_name:
    type: string
    default: 'eu-west-1'

  ssh_key_filename:
    type: string
    default: ~/.ssh/my_keypair.pem

dsl_definitions:
  aws_config: &AWS_CONFIG
    aws_access_key_id: { get_input: aws_access_key_id }
    aws_secret_access_key: { get_input: aws_secret_access_key }
    ec2_region_name: { get_input: aws_region_name }

node_templates:

  my_host:
    type: cloudify.aws.nodes.Instance
    properties:
      aws_config: *AWS_CONFIG
      install_agent: false
      image_id: 'ami-b265c7c1'
      instance_type: m3.medium
    relationships:
      - target: my_keypair
        type: cloudify.aws.relationships.instance_connected_to_keypair
      - target: my_security_group
        type: cloudify.aws.relationships.instance_connected_to_security_group

  my_keypair:
    type: cloudify.aws.nodes.KeyPair
    properties:
      aws_config: *AWS_CONFIG
      resource_id: 'my_keypair'
      private_key_path: { get_input: ssh_key_filename }

  my_security_group:
    type: cloudify.aws.nodes.SecurityGroup
    properties:
      aws_config: *AWS_CONFIG
      description: Security group for my_server
      rules:
        - ip_protocol: tcp
          from_port: 22
          to_port: 22
          cidr_ip: 0.0.0.0/0

outputs:

  My_server:
    description: My server running on AWS
    value:
      Active_Server_IP: { get_attribute: [ my_host, public_ip_address ] }
      keypair_path: { get_property: [ my_keypair, private_key_path ] }
      
{{< /gsHighlight >}}

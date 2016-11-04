<!-- .slide: data-background-image="images/cloudify-logo.svg" data-background-size="contain" -->


Kurzer Umweg:
## TOSCA
### Topology and Orchestration Specification for Cloud Applications

Note: TOSCA is a template language developed by OASIS, a standards
organization, to describe the topology of a cloud and the applications
running on it. A distinguishing characteristic of TOSCA is that it was
designed to be used with any cloud provider or platform, regardless of
underlying technology. When TOSCA is offered by the platform, a single
TOSCA template could be used to launch the same application on
different providers - without modification!


## TOSCA Simple Profile in YAML
Raus mit XML, rein mit YAML!

Note: The original TOSCA specification describes a Domain Specific
Language in XML, meant to be machine and human readable. However,
anyone who has been tasked with editing XML knows that though it is
certainly readable, the jury's still out on whether it is actually
writable by humans.

Luckily for us humans, the TOSCA Simple Profile in YAML 1.0 is in the
final stages of approval. YAML is a more human-friendly language than
XML, with a syntax much easier to read and edit. Thus, the TOSCA
Simple Profile in YAML version aims to be more accessible and concise,
not least of which to speed the adoption of TOSCA itself.


```yaml
tosca_definitions_version: cloudify_dsl_1_2

imports:
  - http://www.getcloudify.org/spec/cloudify/3.3.1/types.yaml

inputs:
  webserver_port:
    default: 8000
  host_ip:
    default: localhost

node_templates:
  host:
    type: cloudify.nodes.Compute
    properties:
      ip: { get_input: host_ip }
      install_agent: false
  http_web_server:
    type: cloudify.nodes.WebServer
    properties:
      port: { get_input: webserver_port }
    relationships:
      - type: cloudify.relationships.contained_in
        target: host
    interfaces:
      cloudify.interfaces.lifecycle:
        create: deploy.py
        delete: uninstall.py

outputs:
  http_url:
    value: { concat: ['http://', { get_property: [ host, ip ] },
                      ':', { get_property: [http_web_server, port] }] }
```

<https://goo.gl/pAeJRe>

Note: <https://github.com/cloudify-examples/simple-python-webserver-blueprint/blob/master/blueprint.yaml>


### Inputs
### Nodes
### Properties
### Relationships
### Outputs


<!-- .slide: data-background-image="images/cloudify-overview.svg" data-background-size="contain" -->


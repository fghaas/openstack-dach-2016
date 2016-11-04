<!-- .slide: data-background-image="images/heat-logo.png" data-background-size="contain" -->


<!-- .slide: data-background-image="images/heat-logo-draft.png" data-background-size="contain" -->


# Heat
ermöglicht uns das Ausrollen
## vollständiger
virtueller Umgebungen


# HOT
ist 100%
# YAML


Was können wir mit
# Heat
machen?


### `OS::Nova::Server`
Konfiguriert Nova-VMs


```yaml
resources:
  mybox:
    type: "OS::Nova::Server"
    properties:
      name: mybox
      image: trusty-server-cloudimg-amd64
      flavor: m1.small
      key_name: mykey
```


Wir könnten diesen Stack jetzt
# starten


```bash
heat stack-create -f stack.yml mystack
```

```bash
openstack stack create -t stack.yml mystack
```


Aber so wäre es nicht besonders
# flexibel


Machen wir das doch besser mit
## Parametern


```yaml
parameters:
  flavor:
    type: string
    description: Flavor to use for servers
    default: m1.medium
  image:
    type: string
    description: Image name or ID
    default: trusty-server-cloudimg-amd64
  key_name:
    type: string
    description: Keypair to inject into newly created servers
```


... und
## eingebauten Funktionen


```yaml
resources:
  mybox:
    type: "OS::Nova::Server"
    properties:
      name: mybox
      image: { get_param: image }
      flavor: { get_param: flavor }
      key_name: { get_param: key_name }
```


Jetzt können wir diese Parameter
## einstellen


```bash
heat stack-create -f stack.yml \
  -P key_name=mykey \
  -P image=cirros-0.3.3-x86_64 \
  mystack
```

```bash
openstack stack create -t stack.yml \
  --parameter key_name=mykey \
  --parameter image=cirros-0.3.3-x86_64 \
  mystack
```


Sorgen wir für
## Netzwerk-Konnektivität


### `OS::Neutron::Net`
### `OS::Neutron::Subnet`
Definiert Neutron-Netzwerke


```yaml
  mynet:
    type: "OS::Neutron::Net"
    properties:
      name: management-net

  mysub_net:
    type: "OS::Neutron::Subnet"
    properties:
      name: management-sub-net
      network: { get_resource: management_net }
      cidr: 192.168.122.0/24
      gateway_ip: 192.168.101.1
      enable_dhcp: true
      allocation_pools: 
        - start: "192.168.101.2"
          end: "192.168.101.50"
```


## `get_resource`
Querverweise zwischen Ressourcen

Automatische Abhängigkeit


### `OS::Neutron::Router`
### `OS::Neutron::RouterGateway`
### `OS::Neutron::RouterInterface`
Konfiguriert Neutron-Router


```yaml
parameters:
  public_net:
    type: string
    description: Public network ID or name

resources:
  router:
    type: OS::Neutron::Router
  router_gateway:
    type: OS::Neutron::RouterGateway
    properties:
      router: { get_resource: router }
      network: { get_param: public_net }
  router_interface:
    type: OS::Neutron::RouterInterface
    properties:
      router: { get_resource: router }
      subnet: { get_resource: mysub_net }
```


### `OS::Neutron::Port`
Konfiguriert Neutron-Ports


```yaml
  mybox_management_port:
    type: "OS::Neutron::Port"
    properties:
      network: { get_resource: mynet }
```

```yaml
  mybox:
    type: "OS::Nova::Server"
    properties:
      name: deploy
      image: { get_param: image }
      flavor: { get_param: flavor }
      key_name: { get_param: key_name }
      networks:
        - port: { get_resource: mybox_management_port }
```


### `OS::Neutron::SecurityGroup`
Konfiguriert Neutron Security Groups


```yaml
  mysecurity_group:
    type: OS::Neutron::SecurityGroup
    properties:
      description: Neutron security group rules
      name: mysecurity_group
      rules:
      - remote_ip_prefix: 0.0.0.0/0
        protocol: tcp
        port_range_min: 22
        port_range_max: 22
      - remote_ip_prefix: 0.0.0.0/0
        protocol: icmp
        direction: ingress
```

```yaml
  mybox_management_port:
    type: "OS::Neutron::Port"
    properties:
      network: { get_resource: mynet }
      security_groups:
        - { get_resource: mysecurity_group }
```


### `OS::Neutron::FloatingIP`
Weist Floating-IP-Adressen zu


```yaml
  myfloating_ip:
    type: "OS::Neutron::FloatingIP"
    properties:
      floating_network: { get_param: public_net }
      port: { get_resource: mybox_management_port }
```


### `outputs`
Gibt Werte oder Attribute zurück


```yaml
outputs:
  public_ip:
    description: Floating IP address in public network
    value: { get_attr: [ myfloating_ip, floating_ip_address ] }
```


```bash
heat output-show \
  mystack public_ip
```

```bash
openstack stack output show \
  mystack public_ip
```


Integration von
# Heat
mit
## `cloud-init`


### `OS::Heat::CloudConfig`
Verwaltet `cloud-config` direkt aus Heat


```yaml
resources:
  myconfig:
    type: "OS::Heat::CloudConfig"
    properties:
      cloud_config:
        package_update: true
        package_upgrade: true
```

```yaml
  mybox:
    type: "OS::Nova::Server"
    properties:
      name: deploy
      image: { get_param: image }
      flavor: { get_param: flavor }
      key_name: { get_param: key_name }
      networks:
        - port: { get_resource: mybox_management_port }
      user_data: { get_resource: myconfig }
      user_data_format: RAW
```


Damit können wir
### `cloud-config`-Parameter
direkt aus Heat setzen


```yaml
parameters:
  # [...]
  username:
    type: string
    description: Additional login username
    default: foobar
  gecos:
    type: string
    description: Additional user full name
    default: ''
```

```yaml
  myconfig:
    type: "OS::Heat::CloudConfig"
    properties:
      cloud_config:
        package_update: true
        package_upgrade: true
        users:
        - default
        - name: { get_param: username }
          gecos: { get_param: gecos }
          groups: "users,adm"
          lock-passwd: false
          passwd: '$6$WP9924IJiLSto8Ng$MSDwCvlT28jM'
          shell: "/bin/bash"
          sudo: "ALL=(ALL) NOPASSWD:ALL"
        ssh_pwauth: true
```

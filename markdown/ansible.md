<!-- .slide: data-background-image="images/ansible-logo.svg" data-background-size="contain" -->


## Ansible 1.x
### eingebaut

**Glance** glance_image **Keystone** keystone_user **Nova**
nova_compute nova_keypair **Neutron**
quantum_floating_ip_associate quantum_floating_ip
quantum_network quantum_router_gateway
quantum_router_interface quantum_router quantum_subnet


## Ansible 2.0
### Shade

**Keystone** os_auth os_user_group os_user **Glance**
os_image_facts os_image **Cinder** os_server_volume
os_volume **Nova** os_keypair os_nova_flavor
os_server_actions os_server_facts os_server **Ironic**
os_ironic_node os_ironic **Neutron** os_floating_ip
os_network os_networks_facts os_port os_router
os_security_group os_security_group_rule os_subnet
os_subnets_facts **Swift** os_object


## `clouds.yml`
```yaml
clouds:
  testing:
    auth:
      auth_url: http://openstack.local:5000/v2.0
      username: admin
      password: secret
      project_name: admin
```


## Glance-Image hochladen
```yaml
- os_image:
    cloud: testing
    name: cirros
    state: present
    disk_format: qcow2
    container_format: bare
    filename: cirros.qcow2
  register: myimage
```


## VM erzeugen
```yaml
- name: create a nova server
  os_server:
    cloud: testing
    name: myserver
    state: present
    nics:
      - net-name: private
    image: "{{ myimage.image.id }}"
    flavor: m1.small
    key_name: my_ssh_key
```


## Dynamic inventory

```bash
ansible-playbook -i openstack.py services.yml
```

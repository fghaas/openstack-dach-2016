<!-- .slide: data-background-image="images/juju-logo.svg" data-background-size="contain" -->


# Juju
# ~~MAAS~~ <!-- .element: class="fragment" -->


## Juju-Provider

### EC2 Azure **OpenStack** Google **Manual** vSphere MAAS Joyent LXD **Rackspace**


## Simplestreams

Note: More information is available at https://jujucharms.com/docs/2.0/howto-privatecloud


### Simplestreams

Metadaten erzeugen

```bash
juju metadata generate-image \
  -d ~/simplestreams \
  -i $IMAGE_ID \
  -s $OS_SERIES \
  -r $REGION \
  -u http://$KEYSTONE_IP:5000/v2.0
```

Note: Keystone v3 support was added in Juju 2.0rc3. This is the
version that ships in Ubuntu yakkety (16.10) and is also available
from a PPA.


### Simplestreams

Metadaten hochladen

```bash
openstack container create simplestreams
swift upload simplestreams *
swift post simplestreams --read-acl .r:*
```


### Simplestreams

Service anlegen

```bash
openstack service create \
  --name product-stream \
  --description “Product Simple Stream” product-streams
```


### Simplestreams

Endpoint anlegen

```bash
openstack endpoint create \
  --region $REGION \
  --publicurl $SWIFT_URL/simplestreams/images \
  --internalurl $SWIFT_URL/simplestreams/images \
  --publicurl $SWIFT_URL/simplestreams/images \
product-streams
```


### Anwendungen ausrollen
```bash
juju deploy -n 3 rabbitmq-server
```


Note: Applications were called "services" in Juju 1.x; the terminology
is changing for 2.x.


## Juju 1.x:

Frustriend nah dran an genial.


## Juju 2.0:

Alles anders.

Ohne Upgradepfad.  <!-- .element: class="fragment" -->
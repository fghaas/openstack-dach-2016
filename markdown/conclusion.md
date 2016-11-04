## Im Vergleich

Note: On the following slides, the check mark (☑) means a feature is
supported, the X mark (☒) means a feature is unsupported — and the
frowny face (☹) means the authors would **like** for the feature to be
fully supported and work well, but in all honesty it really doesn't.


|          | OpenStack-nativ       |
| -------- |:---------------------:|
| Heat     | ☑                    |
| Juju     | ☒                    |
| Ansible  | ☒                    |
| Cloudify | ☒                    |

Note: Of the orchestration tools described here, Heat is the only one
that is built into OpenStack, and as a result, comes along with
it. Heat has had a fairly good track record with onboarding newly
added OpenStack features, albeit with some delay. Case in point:
Neutron LBaasv2 support was only added in Mitaka, whereas v1 was
**dropped** from Neutron in Newton. This does create issues for public
cloud providers not deploying every single OpenStack release.


|          | Stackweite Vorgänge   |
| -------- |:---------------------:|
| Heat     | ☑                    |
| Juju     | ☒                    |
| Ansible  | ☒                    |
| Cloudify | ☒                    |

Note: Heat allows you to apply **actions** to the stack as a whole,
such as suspending and resuming an entire stack. This can be a huge
cost saver in public clouds. No other orchestration facility has this.


|          | Natives Scale-Out     |
| -------- |:---------------------:|
| Heat     | ☑                    |
| Juju     | ☑                    |
| Ansible  | ☒                    |
| Cloudify | ☑                    |

Note: Heat has a dedicated facility to "add more of X", with the
`OS::Heat::ResourceGroup` resource type and parameters, and
auto-scaling groups. Juju has `juju add-unit`, which easily spins up
more instances of a given service, and connects them to already
existing instances. Cloudify also supports native scale-out. Ansible
does not; although scale-out can be achieved simply by running an
additional playbook.


|          | Standardbasierend     |
| -------- |:---------------------:|
| Heat     | ☒                    |
| Juju     | ☒                    |
| Ansible  | ☒                    |
| Cloudify | ☑                    |

Note: Cloudify is the only tool that even attempts to implement an
industry standard (TOSCA). You could also argue that CloudFormation is
a _de facto_ standard, but support for CFN in Heat has lapsed. Neither
Juju nor Ansible attempt to implement any industry standard (YAML is
just a syntax, not a standard).


|          | OpenStack ausrollen   |
| -------- |:---------------------:|
| Heat     | ☑                    |
| Juju     | ☑                    |
| Ansible  | ☑                    |
| Cloudify | ☒                    |

Note: Heat (with Ironic), Juju (with the `manual` and MAAS providers),
and Ansible (through OSA) can be used to deploy OpenStack itself, in
addition to deploying applications _within_ OpenStack.


|          | Anpassbarkeit         |
| -------- |:---------------------:|
| Heat     | ☑                    |
| Juju     | ☹                    |
| Ansible  | ☑                    |
| Cloudify | ☑                    |

Note: Heat (with `CloudConfig` and `SoftwareConfig`), Ansible, and
Cloudify all allow us to customize the applications we run on
OpenStack to our heart's content. Juju, not so much. Juju aims to
codify best practices so won't ever allow you to change _everything,_
it always tries to make some decisions for the user. A lot of people
find that off-putting — it's a bit of an example of "make a tool that
any fool can use, and only a fool will want to use it."


|          | GUI                   |
| -------- |:---------------------:|
| Heat     | ☑                    |
| Juju     | ☑                    |
| Ansible  | ☹                    |
| Cloudify | ☑                    |

Note: Heat does come with Horizon integration. It's not very
user-friendly, but it does exist. Juju has a full GUI and it's
great. Ansible has Ansible Tower, but it hasn't been open sourced more
than a year post-acquisition (hello, Red Hat?), and Cloudify has
Cloudify Manager.


|          | Community             |
| -------- |:---------------------:|
| Heat     | ☑                    |
| Juju     | ☹                    |
| Ansible  | ☑                    |
| Cloudify | ☒                    |

Note: Heat is completely driven by the OpenStack developer
community. Ansible has had major contributions from Rackspace (OSA)
and HP/IBM/Red Hat (i.e. wherever Monty worked at the time, Shade),
but has a diverse developer community. Cloudify is mostly driven by
GigaSpaces. Juju, well they would _like_ lots of community
contributions and they do get them for individual applications, but
both the OpenStack charms and the OpenStack provider are almost 100%
Canonical's work.


|          | Jedes OpenStack       |
| -------- |:---------------------:|
| Heat     | ☹                    |
| Juju     | ☹                    |
| Ansible  | ☑                    |
| Cloudify | ☑                    |

Note: **Matrix slide upcoming**

You _should_ be able to run Heat templates, unmodified, against
just about any OpenStack based public cloud out there. In reality,
Heat support is sometimes lacking, or it may be that the provider
simply omits a core OpenStack feature that your templates rely on
(example: OVH not doing floating IPs). Juju requires that your
provider set up simplestreams metadata. Ansible should run against
anything that exposes standard OpenStack APIs — you may, however, run
into API throttling issues with dynamic inventory. Cloudify, too, only
requires standard public OpenStack APIs.


<!-- .slide: data-background-image="images/matrix.gif" data-background-size="cover" -->



|                        | Heat      | Juju      | Ansible   | Cloudify  |
| ---------------------- |:---------:|:---------:|:---------:|:---------:|
| OpenStack-nativ        | ☑        | ☒        | ☒        | ☒        |
| Stackweite Vorgänge    | ☑        | ☒        | ☒        | ☒        |
| Native Scale-Out       | ☑        | ☑        | ☒        | ☑        |
| Standardbasierend      | ☒        | ☒        | ☒        | ☑        |
| Anpassbarkeit          | ☑        | ☒        | ☑        | ☑        |
| GUI                    | ☑        | ☑        | ☹        | ☑        |
| Community              | ☑        | ☹        | ☑        | ☒        |
| Jedes OpenStack        | ☹        | ☹        | ☑        | ☑        |


Welches nehm ich nun?

```python
print(heat or juju or ansible or cloudify)
True
```


<!-- .slide: data-background-image="images/by-sa.svg" data-background-size="contain" -->

Note: Add conclusion notes here.

---
location: "/documentation/installation.md"
title: "Installation"
---

# {% $markdoc.frontmatter.title %}

## Prerequisites

As introduced earlier, this setup leverages [Vagrant](https://www.vagrantup.com/)
for virtualization, and [Bolt](https://puppet.com/docs/bolt/) for maintenance
automation across nodes.

You first need to install these tools before continuing.

**TLDR:** For macOS with [Homebrew](https://brew.sh/):

```bash
$ brew install homebrew/cask/vagrant
$ brew install puppetlabs/puppet/puppet-bolt
```

Other tools such as Consul, Nomad, Vault, and Docker are not required on your
local machine. They are only needed on remote nodes. They will automatically be
installed and configured in the virtual machines.

## Environment variables

Tweaking HashiBox can be done via environment variables.

These variables can be saved in a `.env` file at the top-level directory of
HashiBox. They will automatically be acknowledged by the `Makefile`. Make sure
to `export` each of them, like this:

```bash
export VAGRANT_PROVIDER=parallels
export UBUNTU_VERSION=20.04-arm64
export VAGRANT_CLIENT_RAM=2048
export VAGRANT_SERVER_RAM=1024
export NOMAD_LICENSE=licensekey
```

### Hypervisors and architectures

HashiBox supports multiple architectures and Vagrant providers.

Supported environment variables are:
- `VAGRANT_PROVIDER`: Set the Vagrant provider. Supports `virtualbox`,
  `vmware_desktop`, `parallels`. Default is `virtualbox`.
- `UBUNTU_VERSION`: Set the Ubuntu version. Supports `20.04` (for AMD64),
  `20.04-arm64` (for ARM64). Default is `20.04`.

The Vagrant environment uses a lot of resources. By default it uses 1Go RAM per
*client* nodes (x3) and 512Mo RAM per *server* nodes (x3). You can change these
resources via the following environment variables:
- `VAGRANT_CLIENT_RAM`: Set the RAM for *client* nodes. Default is `1024`.
- `VAGRANT_CLIENT_CPUS`: Set the CPUs for *client* nodes. Default is `1`.
- `VAGRANT_SERVER_RAM`: Set the RAM for *server* nodes. Default is `512`.
- `VAGRANT_SERVER_CPUS`: Set the CPUs for *server* nodes. Default is `1`.

### Enterprise licenses

Consul, Nomad, and Vault Enterprise are supported as well:
- `CONSUL_LICENSE`: Set the Consul Enterprise license key. If set, Consul
  Enterprise will be downloaded instead of Consul OSS.
- `NOMAD_LICENSE`: Set the Nomad Enterprise license key. If set, Nomad Enterprise
  will be downloaded instead of Nomad OSS.
- `VAULT_LICENSE`: Set the Vault Enterprise license key. If set, Vault Enterprise
  will be downloaded instead of Vault OSS.

## Running for the first time

We now assume you run all commands with the desired environment variables
exported.

The `init` entry of the `Makefile` creates the virtual machines, initializes the
environment, and starts the services, automating the whole process. You can run
it with:

```bash
$ make init
```

We don't detail every steps to keep this guide as light as possible. Take a look
at the `Makefile` and `scripts` directory if you wish to better understand how
it works.

## Verify installation

Vault is not yet initialized. Therefore, Vault health checks don't pass and Nomad
can't properly run since it's configured to integrate with Vault. If we take a
look at the Consul UI, this should look like this:
![Consul Services](/screenshots/consul-init.png)

Given the summary schema in the introduction, here is some information with the
appropriate links for each node:

{% table %}
* Datacenter
* Agent's mode
* IP address
* Link to Consul
* Link to Nomad
* Link to Vault
---
* `us-west-1`
* *server*
* 192.168.60.10
* [:8500](http://192.168.60.10:8500)
* [:4646](http://192.168.60.10:4646)
* [:8200](http://192.168.60.10:8200)
---
* `us-west-1`
* *client*
* 192.168.61.10
* [:8500](http://192.168.61.10:8500)
* [:4646](http://192.168.61.10:4646)
* *n/a*
---
* `us-west-2`
* *server*
* 192.168.60.20
* [:8500](http://192.168.60.20:8500)
* [:4646](http://192.168.60.20:4646)
* [:8200](http://192.168.60.20:8200)
---
* `us-west-2`
* *client*
* 192.168.61.20
* [:8500](http://192.168.61.20:8500)
* [:4646](http://192.168.61.20:4646)
* *n/a*
---
* `us-east-1`
* *server*
* 192.168.60.30
* [:8500](http://192.168.60.30:8500)
* [:4646](http://192.168.60.30:4646)
* [:8200](http://192.168.60.30:8200)
---
* `us-east-1`
* *client*
* 192.168.61.30
* [:8500](http://192.168.61.30:8500)
* [:4646](http://192.168.61.30:4646)
* *n/a*
{% /table %}

{% callout level="warning" iconType="securitySignalDetected" title="Vault initialization" %}
As stated, Nomad currently can't properly run. The links related to Nomad will not
work until [Vault has been initialized](/vault-init).
{% /callout %}

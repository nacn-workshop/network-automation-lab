---
title: Experimenting with Ansible
layout: page
---

Start by installing ansible into the virtual environment you created for NAPALM:

```terminal
(venv) $ pip install ansible
```

Once that is done, verify that you're using the virtual environments version of
ansible by typing `which ansible`.  The output of this command should reference
the executable that has been installed into the virtualenv, e.g.:

```terminal
(venv) $ which ansible
/home/grundler/ansible_venv/bin/ansible
```

Additionally, you will also want to create a file in your home directory named
`.ansible.cfg` with the following content:

```ini
[defaults]
host_key_checking = False
```

This will allow Ansible to not throw an error when it detects an unknown SSH
key.  Next, we need to define an inventory so Ansible knows how to connect to
our network devices.  Ansible inventories can dynamically pull inventory data
from remote sources such as Netbox or Cobbler, but for this workshop we will
simply use a flat file for simplicity.  Create a file named `inventory.ini` in
your current working directory with the following contents:

```ini
```

We can now validate that ansible can correctly parse this a source of inventory
data using the `ansible-inventory` command:

```terminal
```

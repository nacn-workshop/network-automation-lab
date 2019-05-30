---
title: Experimenting with Ansible
layout: page
---

*If you haven't already followed the [setup steps]({{ "setup" | relative_url }}), complete those before continuing with this tutorial.*

The commands in this part of the tutorial need the files in the `ansible` directory: Change to it before running the subsequent commands.

```terminal
$ cd ansible
```

# Validate your inventory

We can validate that our inventory is formatted correctly and contains all the
hosts we expect with the following helper command:

```terminal
{
    "_meta": {
        "hostvars": {
            "eos": {
                "ansible_connection": "network_cli",                                              
                "ansible_host": "127.0.0.1",
                "ansible_hostname": "set-by-ansible",                                             
                "ansible_network_os": "eos",
                "ansible_password": "vagrant",
                "ansible_port": 12201,
                "ansible_user": "vagrant",
                "dns_resolver": "192.168.1.1",
                "domain_name": "workshop.local",                                                  
                "ntp_servers": [
                    "172.16.0.1",
                    "172.16.0.2"
                ]
            }
        }
    },
    "all": {
        "children": [
            "switches",
            "ungrouped"
        ]
    },
    "switches": {
        "hosts": [
            "eos"
        ]
    },
    "ungrouped": {}
}
```

# Run a one-off Ansible command

Ansible has some built-in modules for interacting with EOS devices. We can use
one of them, `eos_info`, to get some information about our virtual device:

```terminal
(venv) $ ansible -i hosts.cfg eos -m eos_facts
eos | SUCCESS =﹥ {
    "ansible_facts": {
        "ansible_net_all_ipv4_addresses": [
            "10.0.2.15"
        ],
        "ansible_net_all_ipv6_addresses": [],
        "ansible_net_api": "cliconf",
        "ansible_net_filesystems": [
            "file:",
            "flash:",
            "system:"
        ],
        "ansible_net_fqdn": "localhost",
        "ansible_net_gather_subset": [
            "hardware",
            "interfaces",
            "default"
        ],
        "ansible_net_hostname": "localhost",
        "ansible_net_image": "flash:/vEOS-lab.swi",
        "ansible_net_interfaces": {
            "Management1": {
                "bandwidth": 1000000000,
                "description": "",
                "duplex": "duplexFull",
                "ipv4": {
                    "address": "10.0.2.15",
                    "masklen": 24
                },
                "lineprotocol": "up",
                "macaddress": "08:00:27:f5:d6:58",
                "mtu": 1500,
                "operstatus": "connected",
                "type": "routed"
            }
        },
        "ansible_net_memfree_mb": 1358.3828125,
        "ansible_net_memtotal_mb": 1969.34765625,
        "ansible_net_model": "vEOS",
        "ansible_net_neighbors": {},
        "ansible_net_python_version": "3.7.3",
        "ansible_net_serialnum": "",
        "ansible_net_system": "eos",
        "ansible_net_version": "4.21.1.1F"
    },
    "changed": false
}
```

Let's break down what's happening with this command:
  - The `ansible` part is the command we're calling. There are several other--more commonly used--commands that we'll see later.
  - The `-i hosts.cfg` part is telling Ansible to use that file for its inventory.
  - The `eos` part is specifying with host to target.
  - The `-m eos_facts` part is telling Ansible to run the eos\_facts module.

We can further expand on this example by running arbitrary show commands with
the `eos_command` module:

```terminal
(venv) $ $ ansible -i hosts.cfg eos -m eos_command -a "commands='show version'"
eos | SUCCESS => {
    "changed": false, 
    "stdout": [
        "Arista vEOS\nHardware version:    \nSerial number:       \nSystem MAC address:  0800.277b.25dd\n\nSoftware image version: 4.21.1.1F\nArchitecture:           i386\nInternal build version: 4.21.1.1F-10146868.42111F\nInternal build ID:      ed3973a9-79db-4acc-b9ac-19b9622d23e2\n\nUptime:                 0 weeks, 0 days, 0 hours and 42 minutes\nTotal memory:           2016612 kB\nFree memory:            1391180 kB"
    ], 
    "stdout_lines": [
        [
            "Arista vEOS", 
            "Hardware version:    ", 
            "Serial number:       ", 
            "System MAC address:  0800.277b.25dd",                                                
            "", 
            "Software image version: 4.21.1.1F",                                                  
            "Architecture:           i386", 
            "Internal build version: 4.21.1.1F-10146868.42111F",                                  
            "Internal build ID:      ed3973a9-79db-4acc-b9ac-19b9622d23e2",                       
            "", 
            "Uptime:                 0 weeks, 0 days, 0 hours and 42 minutes",                    
            "Total memory:           2016612 kB",                                                 
            "Free memory:            1391180 kB"
        ]
    ]
}
```

In this example, we've used the `-a` flag to specify and additional argument to
the `eos_command` module that is the command we want to run.  Also, note in the
return output from ansible, we've recieved the data in two different formats.
In the "stdout" key, we can see the raw string contents of the "show version"
command, as well as a separate "stdout_lines" key that contains the native
output as it would be seen on the EOS CLI.

Running ad-hoc commands in this fashion can be useful for running quick one-off
tasks, but once you start to configure a modules behavior with additional
arguments, using Ansible playbooks becomes a much more manageable solution.
Let's examine what a playbook to perform that same show command looks like:

```yaml
- hosts: eos

  tasks:
    - name: get output of "show version" command
      eos_command:
        commands: "show version"
      register: version

    - name: print results of "show version" command to console
      debug:
        msg: {% raw %}"{{ version }}"{% endraw %}
```

Let's break down what's going on here.
- We're writing this file in the YAML format
- The "hosts" key at the top of this file is indicating which hosts in our
  inventory this playbook applies to.
- the "tasks" key contains a list of Ansible "tasks", e.g. modules, that we are
  invoking on our EOS device.
- In this example, we're using the optional "name" key at the top of each task
  to provide a description about what we're intending to accomplish with each
  task
- We're again invoking the same `eos_command` module, and then specifying our
  `commands` argument as a separate key indented below the module name.
- We're using a task-level optional argument "register" to save the resulting
  output from the `eos_command` module to a variable names "version".
- Finally, we use another module named `debug` to print out the contents of the
  variable named "version" to the console.
- You might have noticed the double-braces surrounding the "version" variable:
  this is how we reference variables in the Jinja2 templating language.  These
  braces tell Ansible that it should treat the name "version" as a variable, and
  to attempt to resolve the name inside the braces to the contents of the output
  of the "show version" command.

Try running this playbook with the following command, and note how it is
functionally equivalent to the previous ad-hoc command.

```terminal
(venv) $ ansible-playbook show_version.yml -i hosts.cfg
```

Now that you're familiar with playbooks, let's do something a little more
interesting.  Recall in our ad-hoc call to Ansible's `get_facts` module, we had
a host-var that was "ansible_hostname" set to a value of "set-by-ansible".  We
can use that variable to configure the hostname of the device itself.  We're
going to use the playbook `change_hostname.yml` in this repo to change the
hostname of our EOS device using of the `eos_config` module:


```terminal
(venv) $ ansible-playbook change_hostname.yml -i hosts.cfg --diff

PLAY [eos] ****************************************************************************************

TASK [ensure hostname matches 'ansible_hostname' in host_vars] ************************************
--- system:/running-config
+++ session:/ansible_1559183494-session-config
@@ -3,6 +3,8 @@
 ! boot system flash:/vEOS-lab.swi
 !
 transceiver qsfp default-mode 4x10G
+!
+hostname set-by-ansible
 !
 spanning-tree mode mstp
 !
changed: [eos]

PLAY RECAP ****************************************************************************************
eos                        : ok=1    changed=1    unreachable=0    failed=0
```

Note that in this case, we passed our ansible-playbook command an additional
flag, `--diff`.  This is a very useful flag when we're using modules that change
the state of a device.  If the device supports it, Ansible can return a diff of
what it actually changed on the remote device.  In this instance, we can see two
lines prefixed with a "+" character, indicating that these lines were added to
the devices' configuration.  Let's take a moment to examine the contents of the
`change_hostname.yml` playbook:

```yaml
- hosts: eos
  gather_facts: False

  tasks:
    - name: ensure hostname matches 'ansible_hostname' in host_vars
      eos_config:
        lines: "hostname {% raw %}{{ ansible_hostname }}{% endraw %}"
```

Note that here we're passing the "lines:" argument to the `eos_config` module,
and that the value of our config lines is another string that is using Jinja2
templating syntax.  In this case, the `ansible_hostname` variable will be
replaced with the variable we manually specified in our `host_vars` file for our
EOS device, resulting in a single config command of "hostname set-by-ansible" as
it would be typed by hand on the device CLI.  Let's make these even more
interesting.  Suppose we would like to configure more than just a single
configuration line?  We could continue to grow our playbook file by adding
additional lines to our `eos_config` module arguments, but it would make more
sense to move those lines to their own file so that we don't have to change the
playbook directly.  All that is requried to do this is to change the "lines:"
argument to "src:" and point that to a template file somewhere:

```yaml
- hosts: eos
  gather_facts: False

  tasks:
    - name: ensure hostname matches 'ansible_hostname' in host_vars
      eos_config:
        src: './ntp_dns_template.j2'
```

Let's see what's going on in that template...


```jinja
{% raw %}{% for server in ntp_servers %}{% endraw %}
{% raw %}ntp server {{ server }}{% endraw %}
{% raw %}{% endfor %}{% endraw %}
{% raw %}ip name-server {{ dns_resolver }}{% endraw %}
```

We're getting a bit more complicated now as we've introduced another useful
Jinja2 construct: looping.  Back up again to our `eos_facts` output, and recall
the value of the "ntp_servers" group_var you saw.  There were two servers listed
here in between square-brackets.  This is a list, and we can loop over each
element of this list using the jinja2 for/endfor syntax.  In this instance, what
we expect to end up with is two NTP servers, and one name server.  Let's run the
playbook and find out:

```terminal
(venv) $ ansible-playbook -i hosts.cfg change_dns_ntp.yml -CD

PLAY [eos] ****************************************************************************************

TASK [configure NTP, DNS settings] ****************************************************************
--- system:/running-config
+++ session:/ansible_1559187925-session-config
@@ -5,6 +5,10 @@
 transceiver qsfp default-mode 4x10G
 !
 hostname set-by-ansible
+ip name-server vrf default 192.168.1.1
+!
+ntp server 172.16.0.1
+ntp server 172.16.0.2
 !
 spanning-tree mode mstp
 !
changed: [eos]

PLAY RECAP ****************************************************************************************
eos                        : ok=1    changed=1    unreachable=0    failed=0
```

et voila!  We have added three lines to our configuration, two additional NTP
servers, and one DNS resolver.

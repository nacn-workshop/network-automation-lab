---
title: Lab Setup
layout: page
---

# Clone Lab Files

To get the files used in these tutorials, clone the project repository:

```terminal
$ git clone https://github.com/nacn-workshop/network-automation-intro-lab-2019.git
```

This will create a directory called `network-automation-intro-lab-2019`. *The rest of the lab instructions will assume you are starting in this directory.*

# Install VirtualBox

VirtualBox is a cross-platform virtualization tool. This will allow us to run virtual servers and devices in a private, virtual lab.

  - [Download and install VirtualBox](https://www.virtualbox.org/)

Once installed, you should be able to check the version from the command line:

```terminal
$ vboxmanage --version
6.0.8r130520
```

Refer to the [VirtualBox documentation](https://www.virtualbox.org/wiki/End-user_documentation) for more information.

# Install Vagrant

Vagrant is a tool that simplifies the process of managing development environments. It will give us a straightforward way to use VirtualBox consistently and easily.

  - [Download and install Vagrant](https://www.vagrantup.com/downloads.html)

Once installed, you should be able to check the version from the command line:

```terminal
$ vagrant version
Installed Version: 2.2.4
Latest Version: 2.2.4

You're running an up-to-date version of Vagrant!
```

Refer to the [Vagrant documentation](https://www.vagrantup.com/docs/index.html) for more information.

# Download vEOS vagrant box

(The instructions in this section are an abbreviated version of the [Setting up the lab](https://napalm.readthedocs.io/en/latest/tutorials/lab.html) section of the Napalm tutorial.)

In order to download the image for an Arista virtual device, you'll have to create an account at <https://www.arista.com/en/user-registration>.

Once you've done that you can download the latest `vEOS-lab-<version>-virtualbox.box` image from <https://www.arista.com/en/support/software-download>. **The examples below use the `vEOS-lab-4.21.1.1F` image, if you download a different version, update the commands to your version.**

Add it to your vagrant box list:

```terminal
$ vagrant box add --name vEOS-lab-4.21.1.1F ~/Downloads/vEOS-lab-4.21.1.1F-virtualbox.box
==﹥ box: Box file was not detected as metadata. Adding it directly...
==﹥ box: Adding box 'vEOS-lab-4.21.1.1F' (v0) for provider:
    box: Unpacking necessary files from: file:///Users/username/Downloads/vEOS-lab-4.21.1.1F-virtualbox.box
==﹥ box: Successfully added box 'vEOS-lab-4.21.1.1F' (v0) for 'virtualbox'!
```

Once the add is complete, you can delete the original file you downloaded:

```terminal
$ rm ~/Downloads/vEOS-lab-4.21.1.1F-virtualbox.box
```

You can list the boxes available to vagrant:

```terminal
$ vagrant box list
```

The output of this command should now include `vEOS-lab-4.21.1.1F  (virtualbox, 0)`.

# Start the virtual device

The lab files include a `Vagrantfile` which defines exactly how to boot the virtual device. Use the vagrant command to start the virtual device:

```terminal
$ vagrant up
```

This command will take a while to run and will produce a lot of output. When it is finished you should have a virtual device running in VirtualBox. Vagrant uses VirtualBox as its default provider, so if VirtualBox is installed you don't need to do anything special to tell Vagrant to use it.

You can use vagrant's `status` command to list the current status of the VMs defined in your Vagrantfile:

```terminal
$ vagrant status
Current machine states:

eos                       running (virtualbox)

This environment represents multiple VMs. The VMs are all listed
above with their current state. For more information about a specific
VM, run `vagrant status NAME`.
```

# Create a Python virtual environment

Next we'll create a virtual environemnt for Python that we'll run directly on our host machine. Python 3 has a built-in package called `venv` that will do this. There is also a stand-alone package called `virtualenv` which does the same thing. This command invokes the built-in package:

```terminal
$ python3 -m venv venv
```

This command creates a directory called "venv" and populates it with the directories and files needed for a Python environment.

```terminal
$ ls venv
bin        include    lib        pyvenv.cfg
```

Whenever you want to use this environment, you'll need to activate it first. But before you activate, take a look at the existing locations of your python command:

```terminal
$ which python3
/usr/local/bin/python3
```

The path you see may be different, but will probably point to a system location.

Now activate the venv:


```terminal
$ source venv/bin/activate
(venv) $
```

Note that your prompt may have changed to add an indicator that you are in an active python venv:

Check the location of your python command again:

```terminal
(venv) $ which python3
/Users/username/network-automation-intro-lab/venv/bin/python3
```

Again what you see will be different, but notice that the path is now pointing to a python3 command in your venv directory, not a system path.

To get out of the venv, use the `deactivate` alias (which is defined in the environment):

```terminal
(venv) $ deactivate
$
```

Activate the venv before continuing:

```terminal
$ source venv/bin/activate
(venv) $
```

Refer to the [venv documentation](https://docs.python.org/3/library/venv.html) for more information.

# Install Napalm

```terminal
(venv) $ pip install napalm
```

Because we a running the command in a venv, the `napalm` package will be installed in the site-packages directory of the venv, not your system Python. List the venv's site-packages to see:

```terminal
$ ls venv/lib/python3.7/site-packages/
Jinja2-2.10.1.dist-info             cryptography-2.6.1.dist-info        netaddr                             pyserial-3.4.dist-info
MarkupSafe-1.1.1.dist-info          easy_install.py                     netaddr-0.7.19.dist-info            requests
PyNaCl-1.3.0.dist-info              future                              netmiko                             requests-2.22.0.dist-info
PyYAML-5.1-py3.7.egg-info           future-0.17.1.dist-info             netmiko-2.3.3-py3.7.egg-info        scp-0.13.2.dist-info
__pycache__                         idna                                nxapi_plumbing                      scp.py
_cffi_backend.cpython-37m-darwin.so idna-2.8.dist-info                  nxapi_plumbing-0.5.2-py3.7.egg-info serial
_yaml.cpython-37m-darwin.so         jinja2                              paramiko                            setuptools
asn1crypto                          jnpr                                paramiko-2.4.2.dist-info            setuptools-40.8.0.dist-info
asn1crypto-0.24.0.dist-info         junos_eznc-2.2.1-py2.7-nspkg.pth    past                                six-1.12.0.dist-info
bcrypt                              junos_eznc-2.2.1.dist-info          pip                                 six.py
bcrypt-3.1.6.dist-info              libfuturize                         pip-19.0.3.dist-info                terminal.py
certifi                             libpasteurize                       pkg_resources                       textfsm-0.4.1-py3.7.egg-info
certifi-2019.3.9.dist-info          lxml                                pyIOSXR                             textfsm.py
cffi                                lxml-4.3.3.dist-info                pyIOSXR-0.53-py3.7.egg-info         texttable.py
cffi-1.12.3.dist-info               markupsafe                          pyasn1                              urllib3
chardet                             nacl                                pyasn1-0.4.5.dist-info              urllib3-1.25.3.dist-info
chardet-3.0.4.dist-info             napalm                              pycparser                           yaml
clitable.py                         napalm-2.4.0.dist-info              pycparser-2.19.dist-info
copyable_regex_object.py            ncclient                            pyeapi
cryptography                        ncclient-0.6.6-py3.7.egg-info       pyeapi-0.8.2-py3.7.egg-info
```

# Install Ansible

```terminal
(venv) $ pip install ansible
```

Once that is done, verify that you're using the venv's version of Ansible. The output of this command should reference the executable that has been installed into the venv:

```terminal
(venv) $ which ansible
/Users/username/network-automation-intro-lab/venv/bin/ansible
```

# Summary

Now that VirtualBox and Vagrant are installed, and we're running a vEOS device, we have a place test configurations and commands.

Our python venv now has napalm and ansible installed. This is where we'll do most of our for in this lab.

# Next

Let's continue to the [Experimenting with Napalm]({{ 'napalm' | relative_url }}) exercise.

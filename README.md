A procedure for creating a Cisco IOS XRv Vagrant box for the [libvirt](https://libvirt.org) provider.

## Prerequisites

  * [Cisco VIRL](http://virl.cisco.com) account
  * [Git](https://git-scm.com)
  * [Python](https://www.python.org)
  * [Ansible](https://docs.ansible.com/ansible/latest/index.html)
  * [libvirt](https://libvirt.org) with client tools
  * [QEMU](https://www.qemu.org)
  * [Expect](https://en.wikipedia.org/wiki/Expect)
  * [sshpass](https://linux.die.net/man/1/sshpass)
  * [Telnet](https://en.wikipedia.org/wiki/Telnet)
  * [Vagrant](https://www.vagrantup.com)
  * [vagrant-libvirt](https://github.com/vagrant-libvirt/vagrant-libvirt)

## Steps

0. Verify the prerequisite tools are installed.

```
which git python ansible libvirtd virsh qemu-system-x86_64 expect sshpass telnet vagrant
vagrant plugin list
```

1. Clone this GitHub repo and _cd_ into the directory.

```
git clone https://github.com/mweisel/cisco-iosxrv-vagrant-libvirt
cd cisco-iosxrv-vagrant-libvirt
```

2. Log in and download the IOS XRv disk image file (iosxrv-k9-demo-6.1.3.qcow2) from your [Cisco VIRL](http://virl.cisco.com) account..

3. Copy (and rename) the disk image file to the `/var/lib/libvirt/images` directory.

```
sudo cp $HOME/Downloads/iosxrv-k9-demo-6.1.3.qcow2 /var/lib/libvirt/images/cisco-iosxrv.qcow2
```

4. Modify the file ownership and permissions. Note the owner will differ between Linux distributions. A couple of examples:

> Arch Linux

```
sudo chown nobody:kvm /var/lib/libvirt/images/cisco-iosxrv.qcow2
sudo chmod u+x /var/lib/libvirt/images/cisco-iosxrv.qcow2
```

> Ubuntu 18.04

```
sudo chown libvirt-qemu:kvm /var/lib/libvirt/images/cisco-iosxrv.qcow2
sudo chmod u+x /var/lib/libvirt/images/cisco-iosxrv.qcow2
```

5. Start the `vagrant-libvirt` network (if not already started).

```
virsh -c qemu:///system net-list
virsh -c qemu:///system net-start vagrant-libvirt
```

6. Run the Ansible playbook. 

```
ansible-playbook main.yml
```

7. Add the Vagrant box. 

```
vagrant box add --provider libvirt --name cisco-iosxrv-6.1.3 ./cisco-iosxrv.box
```

8. Vagrant Up!

```
...
==> R1: Starting domain.
==> R1: Waiting for domain to get an IP address...
==> R1: Waiting for SSH to become available...
==> R1: Configuring and enabling network interfaces...
SSH connection was reset! This usually happens when the machine is
taking too long to reboot. First, try reloading your machine with
`vagrant reload`, since a simple restart sometimes fixes things.
If that doesn't work, destroy your machine and recreate it with
a `vagrant destroy` followed by a `vagrant up`. If that doesn't work,
contact support.
```

> Note

Disregard the `SSH connection was reset!` error. The instance is ready for SSH.

<pre>
$ <b>vagrant ssh R1</b>

RP/0/0/CPU0:xrv#<b>show ssh</b>
Mon Dec 30 05:16:57.705 UTC
SSH version : Cisco-2.0 

id  chan pty     location        state           userid    host                  ver authentication connection type
--------------------------------------------------------------------------------------------------------------------------
Incoming sessions
0   1    vty0    0/0/CPU0        SESSION_OPEN    vagrant   192.168.121.1         v2  rsa-pubkey     Command-Line-Interface 

Outgoing sessions

</pre>

## Debug

To view the telnet session output for the `expect` task:

```
tail -f ~/iosxrv-console.explog
```

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details

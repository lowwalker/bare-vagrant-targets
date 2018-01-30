Bare Vagrant Project
====================

Scaffold to get a project going for ansible development


##### Versions

- OSX 10.13.3
- Virtualbox 5.2.6
- Vagrant 2.0.1
- Ansible 2.4.2.0 (installed on OSX via pip (no sudo))


##### TL;DR
- Clone the repo to your local system and cd into it.
- Checkout a branch for you to beat on.
```
git checkout -b smooth-triceratops
```
- Create a blank role, name it what you like but when creating with this each default file included will have that name in the head of the file.
```
ansible-galaxy init errbody-get-your-role-on
```
- Bring up the 3 targets.
```
vagrant up
```
- Crank out some tasks for your role, update site.yml with your role name and run it.
```
ansible-playbook site.yml
```



### Advanced stuff or maybe something isn't working

##### Vagrantfile options

The default box is Debian 8, below the default box in the vagrantfile you can comment out and uncomment the other two included. The vagrant up process should automatically pull down the box if you don't have it.

However if you want to pull them down individually you can run these commands.

```
vagrant box add ubuntu/trusty64
vagrant box add debian/jessie64
vagrant box add centos/7
```

The second option in the file is the network type. Depending on if you're on a split tunnel VPN or not, you may want to use a bridged IP vs a private. The default is a private network, uncomment the public network line and comment the private line and run vagrant up.

```
srv.vm.network "private_network", ip: servers["ip"]
#      srv.vm.network "public_network", bridge: "en0: Wi-Fi (AirPort)", ip: servers["ip"]
```

When using the public network option you will need to adjust the IP addresses in the inventories/vagrant.yml file to match your local network and those IP's should not be in use.


###### Ansible Provisioners for Vagrantfile

The default block is included, a blank structure. With this you will be able to target by host name or the all group immediately.
```
config.vm.provision :ansible do |ansible|
  ansible.host_key_checking = false
  ansible.playbook = "inventories/provision.yml"
  ansible.become = true
  ansible.host_vars = {}
  ansible.groups = {}

```

Let's consider the below example to see how to add hosts to groups.

```
config.vm.provision :ansible do |ansible|
  ansible.host_key_checking = false
  ansible.playbook = "inventories/provision.yml"
  ansible.become = true
  ansible.host_vars = {
    "target-01" => { "consul_server" => true },
    "target-02" => { "consul_agent" => true },
    "target-03" => { "consul_agent" => true }
  }
  ansible.groups = {
  'consul' => ['target-01', 'target-02','target-03'],
  'consul-agents' => ['target-02','target-03']
  }
```

This gives us two immediately usable ansible features, host vars by individual hosts and groups to target. You can now target all the hosts with the 'consul' group, if you target 'consul-agents' you'll only have target-02 and target-03.

Vagrant will automatically build up the hosts file, so your playbooks or ansible commands can immediately use these as the ansible.cfg in this repo will point at the vagrant created inventory.

###### Leveraging group_vars

An example group_vars directory and file are placed next to the vagrant generated inventory. Simple create a file in the '.vagrant/provisioners/ansible/inventory/group_vars/' directory and ansible will consume it when running playbooks.



##### TODO
- prompt for base box
- prompt for network type

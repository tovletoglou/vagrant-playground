# Vagrant Playground

This is a Vagrant blueprint that creates an Ansible VM server and one or more VMs as playground.

It is using static IP, private network and the same SSH key to all VMs. This way we can SSH seamlessly from one VM to another.<br>
Also, updates hosts file to use the hostnames of the VMs by using the `vagrant-hostmanager` plugin. This apply both on host PC and on VMs<br>
Finally, adds your public SSH key to the guests and combining with static IP of the VMs you can behave them as normal VMs without the need to use vagrant commands; instead of `vagrant ssh VM_NAME` you can do `ssh vagrant@VM_HOSTNAME`.

## Information

On the first vagrant command it will create the SSH key in the `PATH_TO_PROJECT/.ssh` that will be used by the VMs instead of the vagrant-auto generated keys (one for every VM). But be careful! Do that only on test environments!.

Also, it will get your `HOMEDIR/.ssh/id_rsa.pub` public key, and it will add it on the `authorized_keys` on every VM. Adding your public key on the VMs does not affect the security.

Finally, it will update the `hosts` file of your computer, and it will do the same for the clients.

## Requirements

- Oracle VirtualBox (<https://www.virtualbox.org/>)
- Vagrant (<https://www.vagrantup.com/>)
- Vagrant Plugin Hostmanager (to install: `vagrant plugin install vagrant-hostmanager`)
- Terminal with SSH (on Windows <https://git-scm.com/download/win>)
- SSH public and private key under your `HOMEDIR/.ssh/id_rsa` and `HOMEDIR/.ssh/id_rsa.pub`

## Get started

Install `vagrant-hostmanager` plugin

```shell
vagrant plugin install vagrant-hostmanager
```

Clone the project and cd in

```shell
git clone https://github.com/tovletoglou/vagrant-playground.git
cd vagrant-playground
```

Edit the `Vagrantfile` and uncomment the server/s you like to boot (always keep the `ansible.local`).<br>
  The next example boots the `ansible.local` and the `aegir.local`

```ruby
vm_array = Array[
  # ['teamcity.local',  '192.168.33.100'],
  # ['swagger.local',   '192.168.33.90'],
  # ['aegir.local',     '192.168.33.80'],
  # ['web1.local',      '192.168.33.70'],
  # ['web2.local',      '192.168.33.71'],
  # ['web3.local',      '192.168.33.72'],
  # ['galera1.local',   '192.168.33.60'],
  # ['galera2.local',   '192.168.33.61'],
  # ['galera3.local',   '192.168.33.62'],
  # ['elastic.local',   '192.168.33.40'],
  # ['scrapy.local',    '192.168.33.30'],
  # ['haproxy.local',   '192.168.33.50'],
  # ['theseus.local',   '192.168.33.20'],
  # ['jason.local',     '192.168.33.21'],
  # ['asklepios.local', '192.168.33.22'],
  ['ansible.local',   '192.168.33.10']
]
```

It's time to boot up the VMs and wait...

```shell
vagrant up
```

Login to `ansible.local` VM by:

```shell
ssh vagrant@ansible.local
```

Go to the ansible master playbook:

```shell
cd ansible-playbook-vagrant-playground
```

Run the initial playbooks `playbook_ansible` and `playbook_vagrant`:

```shell
ansible-playbook -i hosts playbook_ansible.yml
ansible-playbook -i hosts playbook_vagrant.yml
```

And finally the playbook for the server you want to try. For this example we use the `playbook_aegir`:

```shell
ansible-playbook -i hosts playbook_aegir.yml
```

Now you have and Aegir server to play under `192.168.33.80` and `aegir.local`.

# Vagrant Playground

This is a Vagrant blueprint that creates an Ansible VM server and one or more VMs as a playground.

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
  ['ansible.local',   '192.168.33.10'],

  ['aegir.local',     '192.168.33.20'],
  #
  # ['haproxy.local',   '192.168.33.30'],
  #
  # ['web1.local',      '192.168.33.31'],
  # ['web2.local',      '192.168.33.32'],
  # ['web3.local',      '192.168.33.33'],
  #
  # ['galera1.local',   '192.168.33.41'],
  # ['galera2.local',   '192.168.33.42'],
  # ['galera3.local',   '192.168.33.43'],
  #
  # ['elastic1.local',  '192.168.33.51'],
  # ['elastic2.local',  '192.168.33.52'],
  # ['elastic3.local',  '192.168.33.53'],
  #
  # ['scrapy.local',    '192.168.33.60'],
  #
  # ['teamcity.local',  '192.168.33.70'],
  #
  # ['swagger.local',   '192.168.33.80'],
  #
  # ['wordpress.local', '192.168.33.90'],
]
```

It's time to boot up the VMs and wait...

```shell
vagrant up
```

Login to `ansible.local` VM. It will use your private SSH key stored under your `HOME_DIR/.ssh`:

```shell
ssh vagrant@ansible.local
```

Go to the ansible master playbook:

```shell
cd ansible-playbook-vagrant-playground
```

Run the initial playbooks `playbook_ansible` and `playbook_vagrant`:

```shell
# This playbook will download all the roles used by the included playbooks.
ansible-playbook -i hosts playbook_ansible.yml
# This one will initialize the running VMs. It's not necessary.
ansible-playbook -i hosts playbook_vagrant.yml
```

And finally the playbook for the server you want to try. For this example we use the `playbook_aegir`:

```shell
ansible-playbook -i hosts playbook_aegir.yml
```

Now you have and Aegir server to play under `192.168.33.20` or `aegir.local`.

## Known issues

On Windows systems the hostmanager plugin cannot update the hosts file. If you are experiencing a Ruby error like:

```shell
C:/HashiCorp/Vagrant/embedded/lib/ruby/2.2.0/fileutils.rb:1392:in `initialize': Permission denied @ rb_sysopen - C:/Users/USER/.vagrant.d/tmp/hosts.local (Errno::EACCES)
```

go to the `Vagrantfile` and change the `config.hostmanager.manage_host = true` to `false`.

Then edit manually your `hosts` file under `C:\Windows\System32\drivers\etc`.

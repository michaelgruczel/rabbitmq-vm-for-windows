# rabbitmq-vm-for-windows

vms for rabbit mq on windows on centos.
Unfortunately I have a windows machine .......... but I want to test ansible scripts for rabbitmq for centos7.
if you have a real OS like mac, ubuntu or any unix derivate, you will find easier ways

What you need:

* virtualbox
* vagrant
* vagrant-hostmanager plugin (vagrant plugin install vagrant-hostmanager)


## single node setup

What will happen:

* we will spin up a virtual machine in virtualbox with centos7 and virtualbox guest additions
* we will install rabbitmq including the management plugin and some addtional users
* we will forward the rabbit port to localhost

How to make it possible:

   cd single-node
   vagrant up

How to use it:

* just open http://localhost:15672/ in a browser
* The guest/guest or user1/changeme or user1/changeme
* you can log into the machine with gitbash or cygwin via vagrant ssh, or putty on port 2222 with user vagrant/vagrant
* if you want to use ansible, log into the machine, go to folder /vagrant and fire the scripts against a real vm

## two nodes setup

What will happen:

* we will spin up 2 virtual machines in virtualbox with centos7 and virtualbox guest additions within a private network
* we will install rabbitmq including the management plugin and some addtional users in both vms

How to make it possible:

   cd two-nodes
   vagrant up

How to use it in an independent setup (rabbit nodes not clustered):

* just open http://10.0.0.50:15672 in a browser to open rabbit broker 1
* just open http://10.0.0.51:15672 in a browser to open rabbit broker 2
* The guest/guest or user1/changeme or user1/changeme
* you can log into the machine with gitbash or cygwin via "vagrant ssh rabbit1" and "vagrant ssh rabbit1", or putty on port 2222 and 2200 on 127.0.0.1 with user vagrant/vagrant
* if you want to use ansible, log into one of the machines, go to folder /vagrant and fire the scripts against a real vm

How to use it in a clustered setup (rabbit nodes are clustered):

    vagrant ssh rabbit1
    ping 10.0.0.51
    ping rabbit2
    sudo rabbitmqctl stop_app
    sudo rabbitmqctl join_cluster rabbit@rabbit2
    sudo rabbitmqctl start_app
    sudo rabbitmqctl cluster_status
    exit

    vagrant ssh rabbit2
    ping 10.0.0.50
    ping rabbit1
    sudo rabbitmqctl stop_app
    sudo rabbitmqctl join_cluster rabbit@rabbit1
    sudo rabbitmqctl start_app
    sudo rabbitmqctl cluster_status
    exit

## wow a lot of vagrant and ansible magic, how does that work ?

So to make it simple, vagrant is automation for vitual machines.
It can use for example vmware-player, but i will use virtual box.
The description for your virtual machines are stored in a file called Vagrantfile, 
just some of the most important shell commands:

	# download box if not available in cache, start it, provision it and do networking
	# or just wake up of suspend state
	vagrant up
    
    # freeze state of machine, means suspend state
    vagrant suspend
    
    # stop vm
    vagrant halt
    
    # delete vm
    vagrant destroy

    # more under https://www.vagrantup.com/docs/cli/

Ok but what can we find in the Vagrantfile itself ?

hostmanager:

Vagrant-hostmanager is a Vagrant plugin that manages the hosts file on guest machines. Its goal is to enable resolution of multi-machine environments deployed with a cloud provider where IP addresses are not known in advance. Means instead of using the hostmanager we could just add the entries to the /etc/hosts ourself to fake DNS.

  config.hostmanager.enabled = false
  config.hostmanager.manage_host = true
  config.hostmanager.include_offline = true
  config.hostmanager.ignore_private_ip = false
  ...
  node_config.vm.provision :hostmanager

virtual boxes:

vagrant download boxes and caches them locally.

   # see https://atlas.hashicorp.com/boxes/search for a list of boxes
   # see https://atlas.hashicorp.com/jasonc/boxes/centos7 
   node_config.vm.box = "jasonc/centos7"

In our case we need a centos box with includes guest additions.
That means we will use virtaulbox to shared folders between the guest and host machine.
In linux this is not needed, the default mechanism of vagrant to share folders would work.
Unfortunately we are on windows in this example, so let's use virtual box to share folders

   node_config.vm.synced_folder ".", "/vagrant", type: "virtualbox"
      
we will execute some ansible script within the box.
Normaly we would execute ansible from the host on the box.
Unfortunately we are on windows and installing ansible on windows is painful.
So instead vagrant will install ansible within the box and execute our playbook. 

    config.vm.provision "ansible_local" do |ansible|
      # vvv would be more verbose, default is more or less silent
      ansible.verbose = "v"
      ansible.playbook = "playbook.yaml"
    end

ansible:

ansible is a tool to execute commands on remote servers.
A well defined setup follows some best practices.
So normaly we organize our work in so called roles.
In our case we have just one role called "install-rabbit-mq".
That means ansible expects a folder named "install-rabbit-mq" including a subfolder called tasks.
In that folder he expects a filed called main.yml. In order to trigger that script we just need:

    ---
    - hosts: all
      sudo: true
      roles:
        - install-rabbit-mq





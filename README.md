# rabbitmq-vm-for-windows

What you need:

* virtualbox
* vagrant

What will happen:

* we will spin up a virtual machine in virtualbox with centos7 and virtualbox guest additions
* we will install rabbitmq including the management plugin and some addtional users

How to make it possible:

   vagrant up

How to use it:

* just open http://localhost:15672/ in a browser
* The guest/guest or user1/changeme or user1/changeme
* you can log into the machine with gitbash or cygwin via vagrant ssh, or putty on port 2222 with user vagrant/vagrant
* if you want to use ansible, log into the machine, go to folder /vagrant and fire the scripts against a real vm

Why:

Unfortunately I have a windows machine .......... but I want to test ansible script for centos7.
if you have a real OS like mac, ubuntu or any unix derivate, you will find easier ways
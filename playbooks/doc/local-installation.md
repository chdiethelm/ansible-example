# Local installation


## Requirements

- Ubuntu x86_64 (tested with 14.04)


## Source

Fetch source codes

    git clone https://github.com/stevepereira/webcluster.git
    cd webcluster

## Vagrant

We use [Vagrant](http://www.vagrantup.com) to provide isolated and reproducible development environment for the project.


### Install Virtualbox

    sudo apt-get install virtualbox

**Alternatively** install from Oracle Debian repository [https://www.virtualbox.org/wiki/Linux_Downloads](https://www.virtualbox.org/wiki/Linux_Downloads)

    # Add one of the following lines according to your distribution to your /etc/apt/sources.list:
    # deb http://download.virtualbox.org/virtualbox/debian saucy contrib
    wget -q http://download.virtualbox.org/virtualbox/debian/oracle_vbox.asc -O- | sudo apt-key add -
    sudo apt-get update
    sudo apt-get install virtualbox-4.3

### Install Vagrant from package

Download Vagrant latest 64-bit version for Ubuntu from [vagrantup.com/downloads.html](http://www.vagrantup.com/downloads.html)

    sudo dpkg -i vagrant_1.6.2_x86_64.deb


## Ansible

We use [Ansible](http://www.ansible.com) configuration management to automate provisioning. Ansible 1.5+ is required.

    sudo add-apt-repository ppa:rquillo/ansible
    sudo apt-get update
    sudo apt-get install ansible

**If you are using Windows host, install and use Ansible from inside your virtual machine:**

    vagrant ssh
    sudo apt-get install ansible

### Generate keys for Ansible

Run inside virtual machine:

    ssh-keygen -t rsa

Append generated key into authorized_keys:

    cat /home/vagrant/.ssh/id_rsa.pub >> /home/vagrant/.ssh/authorized_keys

### Run Vagrant and start Ansible installation

Vagrant command looks for Vagrantfile which contains all the virtual machine configurations, therefore you should run the command in the vagrant directory (`cd ytp/vagrant`).

`vagrant up` will both start and then provision your virtual machine. If the machine is already running, this command does nothing. To only start a machine but not provision it, you can use `vagrant up --no-provision`. By default, Vagrant will not reprovision a halted machine that has already been provisioned earlier, but if you wish to start up a completely new machine without provisioning it, you can use the previous command.

`vagrant ssh` connects to the virtual machine over SSH as vagrant user. You can also use conventional ssh command, but this way you do not need to hassle with username, key, IP address and port.

`vagrant provision` will reprovision a running machine.

`vagrant halt` will shutdown a running machine, use `vagrant up` to boot it up again.

`vagrant destroy` will completely remove the virtual machine, requiring you to create a new one with `up` again.

### Advanced provisioning with Ansible

If you need to make adjustments to the provisioning configuration, you can either edit the Ansible settings in the Vagrant file, or simply run Ansible without Vagrant:

    ansible-playbook --inventory-file=vagrant/vagrant-ansible-inventory --user=$USER -v --ask-sudo-pass --ask-pass ansible/single-server.yml --skip-tags=has-hostname,non-local

If you are using ssh keys the following may suffice:

    ansible-playbook --inventory-file=vagrant/vagrant-ansible-inventory --user=$USER -v ansible/single-server.yml --skip-tags=has-hostname,non-local


### Access to service

After the provisioning of the server is ready, access the service at [http://10.10.10.10/](http://10.10.10.10/).


## Known issues

Sometimes the SSH connection breaks.

    "GATHERING FACTS: fatal: [10.10.10.10] => SSH encountered an unknown error during the connection. We recommend you re-run the command using -vvvv, which will enable SSH debugging output to help diagnose the issue"
    or
    "fatal: [10.10.10.10] => decryption failed"

Simply re-run the provision:

    vagrant provision

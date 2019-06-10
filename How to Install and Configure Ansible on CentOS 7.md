# Prerequisites

To follow this tutorial, you will need:

One CentOS 7 server. Follow the steps in [Initial Server Setup with CentOS 7](https://www.digitalocean.com/community/tutorials/initial-server-setup-with-centos-7) to create a non-root user, and make sure you can connect to the server without a password.

## Setup

### Step 1 — Installing Ansible

To begin exploring Ansible as a means of managing our various servers, we need to install the Ansible software on at least one machine.

To get Ansible for CentOS 7, first ensure that the CentOS 7 EPEL repository is installed:

```bash
sudo yum install -y epel-release
```

The install the packages python-pip, python-devel and git

```bash
yum install python-pip python-devel git
```

Once the repository is installed, install Ansible with yum:

```bash
sudo yum install -y ansible
```

We now have all of the software required to administer our servers through Ansible.

### Step 2 — Configuring Ansible Hosts

Ansible keeps track of all of the servers that it knows about through a "hosts" file. We need to set up this file first before we can begin to communicate with our other computers.

Open the file with root privileges like this:

```bash
sudo vi /etc/ansible/hosts

[group_name]
alias ansible_ssh_host=your_server_ip
```

### Step 3 — Using Simple Ansible Commands with Ad-hoc

Ping all of the servers you configured by typing:

```bash
ansible -m ping all
```

Ansible will return output like this:

```bash
Output
host1 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}

host3 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}

host2 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

The all portion means "all hosts." You could just as easily specify a group:

```bash
ansible -m ping servers
```

You can also specify an individual host:

```bash
ansible -m ping host1
```

You can specify multiple hosts by separating them with colons:

```bash
ansible -m ping host1:host2
```

The `shell` module lets us send a terminal command to the remote host and retrieve the results. For instance, to find out the memory usage on our host1 machine, we could use:

```bash
ansible -m shell -a 'free -m' host1
```

As you can see, you pass arguments into a script by using the -a switch. Here's what the output might look like:

```bash
Output
host1 | SUCCESS | rc=0 >>
             total       used       free     shared    buffers     cached
Mem:          3954        227       3726          0         14         93
-/+ buffers/cache:        119       3834
Swap:            0          0          0
```

<img src="/media/kitten.jpg" alt="Kitten"
    title="A cute kitten" />


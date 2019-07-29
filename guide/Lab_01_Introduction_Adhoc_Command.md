# Introduction Ad-Hoc Command

What’s an [ad-hoc](https://docs.ansible.com/ansible/latest/user_guide/intro_adhoc.html) command?

An ad-hoc command is something that you might type in to do something really quick, but don’t want to save for later.

This is a good place to start to understand the basics of what Ansible can do prior to learning the playbooks language – ad-hoc commands can also be used to do quick things that you might not necessarily want to write a full playbook for.

Generally speaking, the true power of Ansible lies in playbooks. Why would you use ad-hoc tasks versus playbooks?

## Parallelism and Shell Command

Let’s use Ansible’s command line tool to reboot all web servers in Atlanta, 10 at a time. First, let’s set up SSH-agent so it can remember our credentials:

```bash
ssh-agent bash
ssh-add ~/.ssh/id_rsa
```

If you don’t want to use ssh-agent and want to instead SSH with a password instead of keys, you can with `--ask-pass` (`-k`), but it’s much better to just use ssh-agent.

Now to run the command on all servers in a group, in this case, _atlanta_, in 10 parallel forks:

```bash
ansible atlanta -a "/sbin/reboot" -f 10
```

`/usr/bin/ansible` will default to running from your user account. If you do not like this behavior, pass in “`-u username`”. If you want to run commands as a different user, it looks like this:

```bash
ansible atlanta -a "/usr/bin/foo" -u username
```

Often you’ll not want to just do things from your user account. If you want to run commands through privilege escalation:

```bash
ansible atlanta -a "/usr/bin/foo" -u username --become [--ask-become-pass]
```

Use `--ask-become-pass` (`-K`) if you are not using a passwordless privilege escalation method (`sudo/su/pfexec/doas/etc`). This will interactively prompt you for the password to use. Use of a passwordless setup makes things easier to automate, but it’s not required.

It is also possible to become a user other than root using `--become-user`:

```bash
ansible atlanta -a "/usr/bin/foo" -u username --become --become-user otheruser [--ask-become-pass]
```

## Using Modules

You can also select what Ansible “module” you want to run. Normally commands also take a `-m` for module name, but the default module name is _‘command’_, so we didn’t need to specify that all of the time. We’ll use `-m` in later examples to run some other Working With Modules.

### Shell

Using the shell module looks like this:

```bash
ansible raleigh -m shell -a 'echo $TERM'
```

### File Transfer

Here’s another use case for the _/usr/bin/ansible_ command line. Ansible can SCP lots of files to multiple machines in parallel.

```bash
ansible atlanta -m copy -a "src=/etc/hosts dest=/tmp/hosts"
```

The file module allows changing ownership and permissions on `files`. These same options can be passed directly to the `copy` module as well:

```bash
ansible webservers -m file -a "dest=/srv/foo/a.txt mode=600"
ansible webservers -m file -a "dest=/srv/foo/b.txt mode=600 owner=mdehaan group=mdehaan"
```

The `file` module can also create directories, similar to `mkdir -p`:

```bash
ansible webservers -m file -a "dest=/path/to/c mode=755 owner=mdehaan group=mdehaan state=directory"
```

As well as delete directories (recursively) and delete files:

```bash
ansible webservers -m file -a "dest=/path/to/c state=absent"
```

### Managing Package

There are modules available for yum and apt. Here are some examples with yum.

Ensure a package is installed, but don’t update it:

```bash
ansible webservers -m yum -a "name=acme state=present"
```

Ensure a package is installed to a specific version:

```bash
ansible webservers -m yum -a "name=acme-1.5 state=present"
```

Ensure a package is at the latest version:

```bash
ansible webservers -m yum -a "name=acme state=latest"
```

Ensure a package is not installed:

```bash
ansible webservers -m yum -a "name=acme state=absent"
```

### Users and Groups

The ‘user’ module allows easy creation and manipulation of existing user accounts, as well as removal of user accounts that may exist:

```bash
ansible all -m user -a "name=foo password=<crypted password here>"
ansible all -m user -a "name=foo state=absent"
```

### Deploying From Source Control

Deploy your webapp straight from git:

```bash
ansible webservers -m git -a "repo=https://foo.example.org/repo.git dest=/srv/myapp version=HEAD"
```

### Managing Services

Ensure a service is started on all webservers:

```bash
ansible webservers -m service -a "name=httpd state=started"
```

Alternatively, restart a service on all webservers:

```bash
ansible webservers -m service -a "name=httpd state=restarted"
```

Ensure a service is stopped:

```bash
ansible webservers -m service -a "name=httpd state=stopped"
```

### Time Limited Background Operations

Long running operations can be run in the background, and it is possible to check their status later. For example, to execute `long_running_operation` asynchronously in the background, with a timeout of 3600 seconds (`-B`), and without polling (`-P`):

```bash
ansible all -B 3600 -P 0 -a "/usr/bin/long_running_operation --do-stuff"
```

If you do decide you want to check on the job status later, you can use the async_status module, passing it the job id that was returned when you ran the original job in the background:

```bash
ansible web1.example.com -m async_status -a "jid=488359678239.2844"
```

Polling is built-in and looks like this:

```bash
ansible all -B 1800 -P 60 -a "/usr/bin/long_running_operation --do-stuff"
```

The above example says “run for 30 minutes max (`-B` 30*60=1800), poll for status (`-P`) every 60 seconds”.

Poll mode is smart so all jobs will be started before polling will begin on any machine. Be sure to use a high enough `--forks` value if you want to get all of your jobs started very quickly. After the time limit (in seconds) runs out (`-B`), the process on the remote nodes will be terminated.

### Gathering Facts

Facts are described in the playbooks section and represent discovered variables about a system. These can be used to implement conditional execution of tasks but also just to get ad-hoc information about your system. You can see all facts via:

```bash
ansible all -m setup
```

It’s also possible to filter this output to just export certain facts, see the “setup” module documentation for details.

# Using Ansible

## Modules

## Inventory

The “inventory” is a configuration file where you define the host information. In the above /etc/ansible/hosts example, we declared two servers under test-hosts.

## Playbooks

In most cases – especially in enterprise environments – you should use Ansible playbooks. A playbook is where you define how to apply policies, declare configurations, orchestrate steps and launch tasks either synchronously or asynchronously on your servers. Each playbook is composed of one or more “plays”. Playbooks are normally maintained and managed in a version control system like Git. They are expressed in YAML (Yet Another Markup Language).

## Plays

Playbooks contain plays. Plays are essentially groups of tasks that are performed on defined hosts to enforce your defined functions. Each play must specify a host or group of hosts. For example, using:

```YML
– hosts: all
```

…we specify all hosts. Note that YML files are very sensitive to white spaces, so be careful!

## Tasks

Tasks are actions carried out by playbooks. One example of a task in an Apache playbook is:

```YML
- name: Install Apache httpd
```

A task definition can contain modules such as yum, git, service, and copy.

## Roles

A role is the Ansible way of bundling automation content and making it reusable. Roles are organizational components that can be assigned to a set of hosts to organize tasks. Therefore, instead of creating a monolithic playbook, we can create multiple roles, with each role assigned to complete a unit of work. For example: a webserver role can be defined to install Apache and Varnish on a specified group of servers.

## Handlers

Handlers are similar to tasks except that a handler will be executed only when it is called by an event. For example, a handler that will start the httpd service after a task installed httpd. The handler is called by the [notify] directive. Important: the name of the notify directive and the handler must be the same.

## Templates

Templates files are based on Python’s Jinja2 template engine and have a .j2 extension. You can, if you need, place contents of your index.html file into a template file. But the real power of these files comes when you use variables. You can use Ansible’s [facts] and even call custom variables in these template files.

## Variables

As the name suggests, you can include custom-made variables in your playbooks. Variables can be defined in five different ways:

1. Variables defined in the play under vars_files attribute:

    ```YML
    vars_files:
    - "/path/to/var/file"
    ```

2. Variables defined in `<role>/vars/apache-install.yml`

3. Variables passed through the command line:

    ```YML
    # ansible-playbook apache-install.yml -e "http-port=80"
    ```

4. Variables defined in the play under vars

    ```YML
    vars: http_port: 80
    ```

5. Variables defined in group_vars/ directory

## Facts

## Vault

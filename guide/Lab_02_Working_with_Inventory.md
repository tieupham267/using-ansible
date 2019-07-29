# Working with Inventory

Ansible works against multiple systems in your infrastructure at the same time. It does this by selecting portions of systems listed in [Ansible’s inventory](https://docs.ansible.com/ansible/latest/user_guide/intro_inventory.html), which defaults to being saved in the location /etc/ansible/hosts. You can specify a different inventory file using the `-i <path>` option on the command line.


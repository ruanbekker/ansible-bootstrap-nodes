# ansible-bootstrap-nodes
Using Ansible to: create user, set public key and updates ssh config for new nodes

## What this does?

- install apt packages
- creates a linux user
- add user to sudoers
- copies the `~/.ssh/id_rsa.pub` public key to the host's authorized_keys file
- updates the ssh config to disable root and password logins. [thanks @khandelwal12nidhi](https://medium.com/@khandelwal12nidhi/setup-ssh-key-and-initial-user-using-ansible-playbook-61eabbb0dba4)

## Tests

New server is provisioned with a root password:

```
$ ansible -i inventory.ini nodes  -m ping -u root --ask-pass
SSH password:
n1 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

## Bootstrap the Nodes

Ensure the correct public key is in place, this setup assumes we are using `~/.ssh/id_rsa.pub`, if you want to change it, edit [roles/copy-public-key/tasks/main.yml](roles/copy-public-key/tasks/main.yml)

```
$ ansible-playbook -i inventory.ini -u root --ask-pass bootstrap_nodes.yml
..
PLAY RECAP *************************************************************************************************************************************************************************************************
n1                         : ok=12   changed=3    unreachable=0    failed=0
```

Verify that you can't ssh with root:

```
$ ssh root@n1
Permission denied (publickey).
```

Now you should be able to authenticate using your ssh private key

# Ansible-playground
This is a repository containing my Ansible configurations that I created when learning this automation tool

Contents of the repo:
- container_config - directory that contains mock configuration of managed nodes.


Useful Ansible commands learned:
1. Verify inventory

```ansible-inventory -i inventory.ini --list```

2. Ping hosts in inventory

```ansible myhosts -m ping -i inventory.ini```

Side workaround:

In order to be able to reach the hosts, we need SSH access to them, so we will generate a SSH key pair on our side and copy the public key to each host.
The first command from below will generate two files:
- docker_key
- docker_key.pub

```
ssh-keygen -t rsa -b 4096 -f ./.ssh/docker_key
```

For each container (connect to them - and install SSH + start the service):
```

apt update
apt install openssh-server
mkdir -p /var/run/sshd
/usr/sbin/sshd -D &
service ssh status
```

From the host machine, copy the public key to each container:
```
docker cp .ssh/docker_key.pub host1c:/root/.ssh/authorized_keys
docker cp .ssh/docker_key.pub host2c:/root/.ssh/authorized_keys
docker cp .ssh/docker_key.pub host3c:/root/.ssh/authorized_keys
```

Also, in order to be able to be able to access the host with SSH, we had to add port mapping in the docker compose file.

I eventually ended up creating a Docker configuration where the control node exists besides the managed nodes.
Same key operation has to be done, but in the case of the nodes.
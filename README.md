# Ansible-playground
This is a repository containing my Ansible configurations that I created when learning this automation tool

Contents of the repo:
- container_config - directory that contains mock configuration of managed nodes.


Useful Ansible commands learned:
1. Verify inventory

```ansible-inventory -i inventory.ini --list```

2. Ping hosts in inventory

```ansible myhosts -m ping -i inventory.ini```
```ansible myhosts -m ping -i inventory.yaml```

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

3. ping datacenter inventory

```ansible datacenter -m ping -i inventory.yaml```

Note: This doesn't work entirely as expected - still need to figure out how to connect to the webservers via SSH without providing a password.

4. Running an ansible playbook

I used the basic Docker configuration to test it:
```ansible-playbook -i inventory.ini playbook.yaml```

5. Setting up an execution environment with Ansible

```
brew install podman
export PATH="/opt/homebrew/anaconda3/bin:$PATH"
pip3 install ansible-navigator
pip3 install ansible-builder
ansible-navigator --version
ansible-builder --version
```

6. Creating an execution environment using Docker (alternatively this can be done with podman)

See `my_first_ee` directory.

```
dorianverna@Dorians-MacBook-Air my_first_ee % ansible-builder build --tag postgresql_ee --container-runtime docker
Running command:
  docker build -f context/Dockerfile -t postgresql_ee context
dorianverna@Dorians-MacBook-Air my_first_ee % docker image ls
REPOSITORY                            TAG       IMAGE ID       CREATED          SIZE
postgresql_ee                         latest    683ca2325ef3   18 seconds ago   323MB
```

Inspecting results:
```
less context/Containerfile
less context/Dockerfile
```

View image details using ansible-navigator:
```
$ ansible-navigator --ce docker
```

7. Running EE against:
 - localhost

```
$ ansible-navigator run test_localhost.yml --execution-environment-image postgresql_ee --mode stdout --pull-policy missing --container-options='--user=0' --ce docker
```

 - remote target

 ```
 TODO - you must test this as well

 ansible-navigator run test_remote.yml -i inventory --execution-environment-image postgresql_ee:latest --mode stdout --pull-policy missing --enable-prompts -u root -k -K --ce docker
 ```

 8. Running Ansible with the community EE image

```
$ ansible-navigator collections --execution-environment-image ghcr.io/ansible-community/community-ee-base:latest --ce=local
$ ansible-navigator exec "ansible localhost -m setup" --execution-environment-image ghcr.io/ansible-community/community-ee-minimal:latest --mode stdout --ce=docker
$ ansible-navigator run test_localhost.yml --execution-environment-image ghcr.io/ansible-community/community-ee-minimal:latest --mode stdout --ce=docker
```

TODO - test the commands above for Mac, they required linux - or test them in a docker container


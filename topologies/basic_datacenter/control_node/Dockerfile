FROM ubuntu:20.04

# Install SSH
RUN apt-get update && apt-get install -y openssh-server

# Create SSH directory
RUN mkdir /var/run/sshd

# Set root password
RUN echo 'root:password' | chpasswd
RUN adduser 'my_server_user'
RUN echo 'my_server_user:password' | chpasswd

# SSH configuration
RUN sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config
RUN sed -i 's/#PubkeyAuthentication yes/PubkeyAuthentication yes/' /etc/ssh/sshd_config
RUN apt-get update
RUN apt-get install net-tools
RUN apt-get install iputils-ping -y
RUN apt-get install pip -y
RUN pip install ansible
# RUN apt-get install vim -y

# Create .ssh directory and copy your public key
RUN mkdir -p /root/.ssh
COPY .ssh/docker_key /root/.ssh
COPY .ssh/docker_key.pub /root/.ssh
COPY .ssh/docker_key_webserver.pub /root/.ssh
COPY .ssh/config /root/.ssh
RUN mkdir -p /home/my_server_user/.ssh
COPY .ssh/docker_key /home/my_server_user/.ssh
COPY .ssh/docker_key.pub /home/my_server_user/.ssh
COPY .ssh/docker_key_webserver.pub /home/my_server_user/.ssh
COPY .ssh/config /home/my_server_user/.ssh
COPY control_node/inventory.yaml /root/ansible/inventory.yaml
RUN chmod 600 /root/.ssh/docker_key
RUN chmod 644 /root/.ssh/docker_key.pub
RUN chmod 600 /home/my_server_user/.ssh/docker_key
RUN chmod 644 /home/my_server_user/.ssh/docker_key.pub

# Expose SSH port
EXPOSE 22

# Start SSH service
CMD ["/usr/sbin/sshd", "-D"]
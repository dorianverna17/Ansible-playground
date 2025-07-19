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
RUN sed -i 's/#PubkeyAuthentication yes/PubkeyAuthentication yes/' /etc/ssh/sshd_config
RUN sed -i 's/#AuthorizedKeysFile/AuthorizedKeysFile/' /etc/ssh/sshd_config
RUN apt-get update
RUN apt-get install net-tools
RUN apt-get install iputils-ping -y

# Create .ssh directory and copy your public key
RUN mkdir -p /root/.ssh
COPY .ssh/docker_key.pub /root/.ssh/authorized_keys
RUN chmod 700 /root/.ssh && chmod 600 /root/.ssh/authorized_keys

# These lines make sense only for the webserver nodes
RUN mkdir -p /home/my_server_user/.ssh
COPY .ssh/docker_key.pub /home/my_server_user/.ssh/authorized_keys
COPY .ssh/docker_key_webserver.pub /home/my_server_user/.ssh/authorized_keys
RUN chmod 700 /home/my_server_user/.ssh && chmod 600 /home/my_server_user/.ssh/authorized_keys

# Expose SSH port
EXPOSE 22

# Start SSH service
CMD ["/usr/sbin/sshd", "-D"]
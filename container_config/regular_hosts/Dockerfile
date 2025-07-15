FROM ubuntu:20.04

# Install SSH
RUN apt-get update && apt-get install -y openssh-server

# Create SSH directory
RUN mkdir /var/run/sshd

# Set root password
RUN echo 'root:password' | chpasswd

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

# Expose SSH port
EXPOSE 22

# Start SSH service
CMD ["/usr/sbin/sshd", "-D"]
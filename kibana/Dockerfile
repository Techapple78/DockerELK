FROM ubuntu:16.04

# Update apt-cache
RUN apt-get update

# Install Ansible
RUN apt-get -y install git software-properties-common && \
    apt-add-repository ppa:ansible/ansible && \
    apt-get update && \
    apt-get -y install ansible

# Copy Ansible Playbook
COPY kibana.playbook.yml /kibana.playbook.yml

# Run Ansible playbook
RUN ansible-playbook -i "localhost," -c local /kibana.playbook.yml

# Cleanup
RUN apt-get -y clean && \
    apt-get -y autoremove && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

ENV PATH /opt/kibana/bin:$PATH

# Setup entrypoint Ansible Playbook
COPY docker-entrypoint.yml /docker-entrypoint.yml

# Setup entrypoint script
COPY docker-entrypoint.sh /usr/local/bin/
RUN chmod +x /usr/local/bin/docker-entrypoint.sh
RUN ln -s usr/local/bin/docker-entrypoint.sh /entrypoint.sh

ENTRYPOINT ["docker-entrypoint.sh"]

# Expose port(s)
EXPOSE 5601

# Container start-up
CMD ["kibana"]

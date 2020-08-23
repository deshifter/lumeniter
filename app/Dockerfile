#
#--------------------------------------------------------------------------
# Image Setup
#--------------------------------------------------------------------------
#

ARG PHP_VERSION
FROM deshifter/php-fpm:${PHP_VERSION}

LABEL maintainer="Artem Fufaldin <artem.fufaldin@gmail.com>"

# Set Environment Variables
ENV DEBIAN_FRONTEND noninteractive

# Add a non-root user to prevent files being created with root permissions on host machine.
ARG PUID=1000
ENV PUID ${PUID}
ARG PGID=1000
ENV PGID ${PGID}

RUN groupadd -g ${PGID} lumen && \
    useradd -u ${PUID} -g lumen -m lumen -G docker_env && \
    usermod -p "*" lumen -s /bin/bash

# Set Timezone
ARG TZ=UTC
ENV TZ ${TZ}

RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

# User Aliases
COPY ./aliases.sh /root/aliases.sh
COPY ./aliases.sh /home/lumen/aliases.sh

RUN sed -i 's/\r//' /root/aliases.sh && \
    sed -i 's/\r//' /home/lumen/aliases.sh && \
    chown lumen:lumen /home/lumen/aliases.sh && \
    echo "" >> ~/.bashrc && \
    echo "# Load Custom Aliases" >> ~/.bashrc && \
    echo "source ~/aliases.sh" >> ~/.bashrc && \
	echo "" >> ~/.bashrc

USER lumen

RUN echo "" >> ~/.bashrc && \
    echo "# Load Custom Aliases" >> ~/.bashrc && \
    echo "source ~/aliases.sh" >> ~/.bashrc && \
	echo "" >> ~/.bashrc

# Composer
USER root

# Add the composer.json
COPY ./composer.json /home/lumen/.composer/composer.json

# Make sure that ~/.composer belongs to lumen
RUN chown -R lumen:lumen /home/lumen/.composer

# Export composer vendor path
RUN echo "" >> ~/.bashrc && \
    echo 'export PATH="$HOME/.composer/vendor/bin:$PATH"' >> ~/.bashrc

USER lumen

# Global install
RUN composer global install

# Export composer vendor path
RUN echo "" >> ~/.bashrc && \
    echo 'export PATH="~/.composer/vendor/bin:$PATH"' >> ~/.bashrc

# Final Touch

RUN rm -f /etc/service/sshd/down

# Regenerate SSH host keys. baseimage-docker does not contain any, so you
# have to do that yourself. You may also comment out this instruction; the
# init system will auto-generate one during boot.
RUN /etc/my_init.d/00_regen_ssh_host_keys.sh
RUN /usr/sbin/enable_insecure_key

## Install an SSH of your choice.
COPY your_key.pub /tmp/your_key.pub
RUN cat /tmp/your_key.pub >> /root/.ssh/authorized_keys && rm -f /tmp/your_key.pub

# Clean up
RUN apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* && \
    rm /var/log/lastlog /var/log/faillog

# Set default work directory
WORKDIR /var/www

FROM php:7.1-cli

# Run as root
USER root:root

# Configure the system
RUN apt-get update \
 && apt-get install -y --no-install-recommends \
 	curl \
 	git \
 	openssh-client \
	wget \
	zlib1g-dev

# Create a custom user to run the container on
RUN useradd php \
 && mkdir -p /home/php \
 && touch /home/php/.profile \
 && chown -R php:php /home/php \
 && mkdir /data \
 && chown -R php:php /data

# The directory to work from
WORKDIR /data

# The volume that can be mounted
VOLUME /data

# Install the zip extension
RUN docker-php-ext-install zip \
 && docker-php-ext-enable zip

# Add the composer installer script
ADD usr/local/bin/install-composer.sh /usr/local/bin/install-composer.sh
ADD usr/local/bin/docker-image-entrypoint.sh /usr/local/bin/docker-image-entrypoint

# Install Composer
RUN chmod +x /usr/local/bin/install-composer.sh \
 && /usr/local/bin/install-composer.sh \
 && mv composer.phar /usr/local/bin/composer \
 && composer self-update \
 && chown php:php /usr/local/bin/composer \
 && rm /usr/local/bin/install-composer.sh

RUN chmod +x /usr/local/bin/docker-image-entrypoint

# Add Tini
ENV TINI_VERSION v0.17.0
ADD https://github.com/krallin/tini/releases/download/${TINI_VERSION}/tini /sbin/tini
RUN chmod +x /sbin/tini

# Run the container as the configured user
USER php:php

# Execute composer
ENTRYPOINT ["/usr/local/bin/docker-image-entrypoint"]

# The default command to execute
CMD ["composer"]

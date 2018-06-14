FROM ubuntu:16.04

MAINTAINER Vaios Tsegkos <tsegkos@mail.com>


# Get user id and group id from arguments
ARG UID
ARG GID
# Get root password.
ARG SERVER_ROOT_PASSWORD

# Install Apache.
RUN apt-get update && apt-get install -y --no-install-recommends apache2 curl git unzip ca-certificates wget nano mysql-client supervisor openssh-server && \
  apt-get remove make && apt-get install make

# Install PHP 7.
RUN apt-get install -y --no-install-recommends php php-dev php-curl php-cli libapache2-mod-php php-mcrypt \
  php-mysql php-mbstring php7.0-xml php7.0-gd php-xdebug php7.0-sqlite3 php7.0-zip php7.0-bcmath
# Configuration.
ADD xdebug /tmp/xdebug
RUN cat /tmp/xdebug >> /etc/php/7.0/apache2/conf.d/20-xdebug.ini && rm /tmp/xdebug

# Install ionCube PHP 7
COPY config/ioncube_loader_lin_7.0.so /usr/lib/php/20151012
COPY config/00-ioncube.ini /etc/php/7.0/apache2/conf.d/

# Remove package lists.
RUN apt-get clean && rm -rf /var/cache/apt/* && rm -rf /var/lib/apt/lists/*

# Enable apache modules.
RUN a2enmod rewrite


# Create user, vhosts, permissions.
# Change user apache runs.
RUN sed -i -e"s/^export\s*APACHE_RUN_USER=www-data/export APACHE_RUN_USER=serveruser/" /etc/apache2/envvars && \
  sed -i -e"s/^export\s*APACHE_RUN_GROUP=www-data/export APACHE_RUN_GROUP=serveruser/" /etc/apache2/envvars

# php.ini for PHP 7
COPY config /etc/php/7.0/apache2

# php.ini for PHP 7.2
#COPY config /etc/php/7.2/apache2

# Create user.
RUN groupadd -r serveruser -g $GID && useradd -ms /bin/bash serveruser -u $UID -g $GID

# Install composer.
RUN cd ~ && curl -sS https://getcomposer.org/installer -o composer-setup.php && \
  php composer-setup.php --install-dir=/usr/local/bin --filename=composer && \
  rm -rf composer-setup.php && \
  echo "PATH=$PATH:~/.composer/vendor/bin" >> ~/.bashrc

# Install phpunit.
RUN wget https://phar.phpunit.de/phpunit.phar && \
  chmod +x phpunit.phar && \
  mv phpunit.phar /usr/local/bin/phpunit


# Add vhosts and enable sites.
COPY vhosts /etc/apache2/sites-available
RUN cd /etc/apache2/sites-available && a2ensite * && a2dissite default-ssl 000-default

# Setup ssh.
RUN mkdir /var/run/sshd && \
  echo 'root:tse6079280783!' | chpasswd && \
  sed -i 's/prohibit-password/yes/' /etc/ssh/sshd_config

# Setup supervisor to run child processes.
ADD ./supervisor/supervisord.conf /etc/supervisor/conf.d/supervisord.conf
ADD ./supervisor/apache.conf /etc/supervisor/conf.d/apache.conf
ADD ./supervisor/ssh.conf /etc/supervisor/conf.d/ssh.conf
RUN chown root:root /etc/supervisor/conf.d/* && chmod 644 /etc/supervisor/conf.d/*

EXPOSE 80 22

CMD ["/usr/bin/supervisord", "-n"]

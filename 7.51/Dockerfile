FROM drupal:7.51-apache

MAINTAINER turtleislan@gmail.com



# custom the PHP extensions for drupal
RUN apt-get update && apt-get install -y libpng12-dev libjpeg-dev libpq-dev git \
	# PHP7 builtin opcache
  #&& pecl install opcache \
	&& docker-php-ext-install sockets \
	&& docker-php-ext-install opcache \
	&& docker-php-ext-enable  opcache 
  
  
  
# install require
RUN apt-get -y update && apt-get -y install vim drush net-tools mysql-client libapache2-mod-rpaf netcat
#RUN drush dl -y --destination=sites/all/modules/contrib ctools module_filter devel admin_menu  l10n_update l10n_client 
# netcat  detect mysql start up



# Reverse proxy add forward, fix remote IP Address
# https://github.com/gnif/mod_rpaf
# https://www.howtoforge.com/tutorial/how-to-install-nginx-as-reverse-proxy-for-apache-on-ubuntu-15-10/#step-configure-logging
# RUN apt-get -y install libapache2-mod-rpaf





# install pecl uploadprogress  fail in PHP7
#RUN pecl install uploadprogress
  #&& docker-php-ext-install uploadprogress 
  #&& docker-php-ext-enable  uploadprogress 
  #&& echo "extension=uploadprogress.so" > /etc/php5/mods-available/uploadprogress.ini \
  #&& echo "extension=uploadprogress.so" > /usr/local/etc/php/conf.d/docker-php-ext-uploadprogress.ini
  
  
# compile uploadprogress for PHP7 #
RUN git clone https://github.com/Jan-E/uploadprogress.git /tmp/php-uploadprogress && \
        cd /tmp/php-uploadprogress && \
        phpize && \
        ./configure --prefix=/usr && \
        make && \
        make install && \
        echo 'extension=uploadprogress.so' > /usr/local/etc/php/conf.d/docker-php-ext-uploadprogress.ini && \
        rm -rf /tmp/*



COPY ./config/default.settings.php  /var/www/html/sites/default
COPY ./config/wait-for-mysql.sh  /var/www/html
COPY ./config/drupal-foreground  /usr/local/bin/
COPY ./config/rpaf.conf  /etc/apache2/mods-available/rpaf.conf

RUN ls -al /usr/local/bin \
  && chmod 755 /usr/local/bin/drupal-foreground \
  && chmod 755 /var/www/html/wait-for-mysql.sh

#backup default sites to tar
RUN chown -Rcf www-data:www-data . \
  && chmod -Rcf 777 ./sites \
  && tar -cf sites.tar ./sites \
  && rm -rf /var/lib/apt/lists/* \
  && chmod 755 /usr/local/bin/  \
  && a2enmod ssl \
  && a2ensite default-ssl \
  && mkdir /etc/apache2/ssl \
  && chmod -Rvf 600 /etc/apache2/ssl





  
#RUN ls -al /usr/local/etc/php/conf.d/

EXPOSE 80 443

CMD ["drupal-foreground"]

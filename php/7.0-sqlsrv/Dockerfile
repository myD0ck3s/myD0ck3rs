###################################################
# myD0ck3rs 1.0
# Debian Jessie as Base Image (Dotdeb PHP builds)
###################################################

FROM debian:jessie

# Repository/Image Maintainer
MAINTAINER myD0ck3r <myd0ck3r@gmail.com>

# Variables for enabling NewRelic
ENV NR_ENABLED=false \
    NR_APP_NAME="" \
    NR_LICENSE_KEY=""

# Add the ENTRYPOINT script
ADD start.sh /scripts/start.sh

# Install Locales, Build Tools, PHP From DotDeb, Common Extensions, Composer and then cleanup
RUN echo "---> Changes and updates for apt packages sources.list" && \    
    echo '# debian-testing packages\n \
    deb http://ftp.us.debian.org/debian testing main contrib non-free\n \
    # debian-source packages\n \
    deb-src http://httpredir.debian.org/debian/ jessie main\n \
    deb-src http://security.debian.org/ jessie/updates main\n' \
    >> /etc/apt/sources.list.d/testingsrc.list && \
    
    echo "---> Creating APT Preferences for g++-5" && \ 
    echo 'Package: g++-5\n \
    Pin: release a=testing\n \
    Pin-Priority: 100\n' \
    >> /etc/apt/preferences.d/g++-5 && \
    
    echo "---> Updating Repository" && \
    apt-get update -y && \
    
    echo "---> Upgrade Distro" && \
    apt-get upgrade -y && \
    
    echo "---> Install Build Dependencies and Tools" && \
    apt-get install -y curl \
    wget \
    nano \
    vim \
    build-essential \
    unzip \
    apt-utils && \
    
    echo "---> Installing g++-5 Testing Version" && \
    export CPPFLAGS="-DSIZEOF_LONG_INT=8" && \
    export DEBIAN_FRONTEND=noninteractive && \
    apt-get -y -qq install -t testing g++-5 && \
    
    echo "---> Configure timezone and locale" && \
    apt-get install -y locales && \
    echo "America/Sao_Paulo" > /etc/timezone && \
    dpkg-reconfigure -f noninteractive tzdata && \
    sed -i -e 's/# en_US.UTF-8 UTF-8/en_US.UTF-8 UTF-8/' /etc/locale.gen && \
    echo 'LANG="en_US.UTF-8"'>/etc/default/locale && \
    dpkg-reconfigure --frontend=noninteractive locales && \
    update-locale LANG=en_US.UTF-8 && \
    
    echo "---> Downloading and Extract unixODBC 2.3.4" && \
    rm -rf /tmp/msodbcdebian && \
    mkdir /tmp/msodbcdebian && \
    wget ftp://ftp.unixodbc.org/pub/unixODBC/unixODBC-2.3.4.tar.gz -P /tmp/msodbcdebian && \
    cd /tmp/msodbcdebian && \
    tar -xzf /tmp/msodbcdebian/unixODBC-2.3.4.tar.gz && \
    cd /tmp/msodbcdebian/unixODBC-2.3.4/ && \

    echo "---> Configuring, Make and Installing the unixODBC 2.3.4" && \
    ./configure --prefix=/usr --libdir=/usr/lib64 --sysconfdir=/etc --disable-gui --disable-drivers --enable-iconv --with-iconv-char-enc=UTF8 --with-iconv-ucode-enc=UTF16LE && \
    make && \
    make install && \
    
    echo "---> Installing Microsoft ODBC Driver 13 for SQL Server Dependencies" && \
    apt-get -y install libssl1.0.0 \
    libgss3 && \
    echo "/usr/lib64" | tee -a /etc/ld.so.conf && \
    ldconfig && \
    
    echo "---> Downloading the Microsoft ODBC Driver 13 for SQL Server - Linux" && \
    cd /tmp/msodbcdebian && \
    wget -nv -O msodbcsql-13.0.0.0.tar.gz "https://meetsstorenew.blob.core.windows.net/contianerhd/Ubuntu%2013.0%20Tar/msodbcsql-13.0.0.0.tar.gz?st=2016-10-18T17%3A29%3A00Z&se=2022-10-19T17%3A29%3A00Z&sp=rl&sv=2015-04-05&sr=b&sig=cDwPfrouVeIQf0vi%2BnKt%2BzX8Z8caIYvRCmicDL5oknY%3D" && \
    tar -zxvf msodbcsql-13.0.0.0.tar.gz && \
    cd /tmp/msodbcdebian/msodbcsql-13.0.0.0/ && \
    ldd /tmp/msodbcdebian/msodbcsql-13.0.0.0/lib64/libmsodbcsql-13.0.so.0.0 && \
    
    echo "---> Configuring and Installing the Microsoft ODBC Driver 13 for SQL Server - Linux" && \
    sed -i "s/uname -p/uname -m/g" /tmp/msodbcdebian/msodbcsql-13.0.0.0/install.sh && \
    sed -i "/req_dm_ver/s/2.3.1/2.3.4/g" /tmp/msodbcdebian/msodbcsql-13.0.0.0/install.sh && \
    ./install.sh install --force --accept-license && \
    
    echo "---> Removing Testing and Sources Repositories" && \
    rm -rf /etc/apt/sources.list.d/testingsrc.list && \
    rm -rf /etc/apt/preferences.d/g++-5 && \
    
    echo "---> Cleaning up APT Cache, Remove Build Dependencies and Tools" && \
    apt-get purge -y --auto-remove g++-5 \
    apt-utils \
    build-essential \
    nano \
    vim && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /usr/share/doc/*
    
RUN echo "---> Adding DotDeb Source List" && \ 
    echo "deb http://packages.dotdeb.org jessie all" > /etc/apt/sources.list.d/dotdeb.list && \
    curl -sS https://www.dotdeb.org/dotdeb.gpg | apt-key add - && \
    apt-get update -y && \
    
    echo "---> Preparing and Installing PHP" && \
    apt-get -y install \
    php7.0-apcu \
    php7.0-bz2 \
    php7.0-cli \
    php7.0-curl \
    php7.0-fpm \
    php7.0-gd \
    php7.0-imagick \
    php7.0-imap \
    php7.0-intl \
    php7.0-json \
    php7.0-mbstring \
    php7.0-mcrypt \
    php7.0-mysql \
    php7.0-mongodb \
    php7.0-opcache \
    php7.0-pgsql \
    php7.0-redis \
    php7.0-soap \
    php7.0-sqlite3 \
    php7.0-xdebug \
    php7.0-xml \
    php7.0-zip \
    php7.0-phpdbg && \
    
    echo "---> Installing PDO Extensions for SQL Server" && \
    mkdir -p /tmp/msodbcdebian && \
    cd /tmp/msodbcdebian && \
    wget https://github.com/Microsoft/msphpsql/releases/download/v4.0.5-Linux/Ubuntu15.zip && \
    unzip Ubuntu15.zip && \
    cp ./Ubuntu15/php_pdo_sqlsrv_7_nts.so /usr/lib/php/20151012/ && \
    cp ./Ubuntu15/php_sqlsrv_7_nts.so /usr/lib/php/20151012/ && \
    echo -e "; priority=20\nextension=php_pdo_sqlsrv_7_nts.so" > /etc/php/7.0/mods-available/pdo_sqlsrv.ini && \
    echo -e "; priority=20\nextension=php_sqlsrv_7_nts.so" > /etc/php/7.0/mods-available/sqlsrv.ini && \
    phpenmod pdo_sqlsrv && \
    phpenmod sqlsrv && \
    
    echo "---> Installing Composer" && \
    curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer && \
    
    echo "---> Cleaning up APT Cache, Remove Build Dependencies and Tools" && \
    apt-get purge -y --auto-remove unzip && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /usr/share/doc/*

RUN echo "---> Adding the php-user" && \
    adduser --disabled-password --gecos "" php-user && \
    mkdir -p /var/www/app && \
    chown -R php-user:php-user /var/www

RUN echo "---> Configuring PHP settings" && \
    echo "php-user  ALL = ( ALL ) NOPASSWD: ALL" >> /etc/sudoers && \
    sed -i "/user = .*/c\user = php-user" /etc/php/7.0/fpm/pool.d/www.conf && \
    sed -i "/group = .*/c\group = php-user" /etc/php/7.0/fpm/pool.d/www.conf && \
    sed -i "/listen-user = .*/c\listen-user = php-user" /etc/php/7.0/fpm/pool.d/www.conf && \
    sed -i "/listen-group = .*/c\listen-group = php-user" /etc/php/7.0/fpm/pool.d/www.conf && \
    sed -i "/listen = .*/c\listen = [::]:9000" /etc/php/7.0/fpm/pool.d/www.conf && \
    sed -i "/;access.log = .*/c\access.log = /proc/self/fd/2" /etc/php/7.0/fpm/pool.d/www.conf && \
    sed -i "/;clear_env = .*/c\clear_env = no" /etc/php/7.0/fpm/pool.d/www.conf && \
    sed -i "/;catch_workers_output = .*/c\catch_workers_output = yes" /etc/php/7.0/fpm/pool.d/www.conf && \
    sed -i "/pid = .*/c\;pid = /run/php/php7.0-fpm.pid" /etc/php/7.0/fpm/php-fpm.conf && \
    sed -i "/;daemonize = .*/c\daemonize = yes" /etc/php/7.0/fpm/php-fpm.conf && \
    sed -i "/error_log = .*/c\error_log = /proc/self/fd/2" /etc/php/7.0/fpm/php-fpm.conf && \
    sed -i "/post_max_size = .*/c\post_max_size = 1000M" /etc/php/7.0/fpm/php.ini && \
    sed -i "/upload_max_filesize = .*/c\upload_max_filesize = 1000M" /etc/php/7.0/fpm/php.ini && \
    sed -i "/zend_extension=xdebug.so/c\;zend_extension=xdebug.so" /etc/php/7.0/mods-available/xdebug.ini
    
RUN echo "---> Adding Support for NewRelic" && \
    mkdir /tmp/newrelic && \
    cd /tmp/newrelic && \
    wget -r -l1 -nd -A"linux.tar.gz" https://download.newrelic.com/php_agent/release/ && \
    gzip -dc newrelic*.tar.gz | tar xf - && \
    cd newrelic-php5* && \
    rm -f /usr/lib/php/20151012/newrelic.so && \
    cp ./agent/x64/newrelic-20151012.so /usr/lib/php/20151012/newrelic.so && \
    cp ./daemon/newrelic-daemon.x64 /usr/bin/newrelic-daemon && \
    cp ./scripts/newrelic.ini.template /scripts/newrelic.ini && \
    mkdir /var/log/newrelic && \
    chown -R php-user:php-user /var/log/newrelic && \
    chmod +x /scripts/start.sh && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /usr/share/doc/*

# Dumb Init
ADD https://github.com/krallin/tini/releases/download/v0.10.0/tini /tini
RUN chmod +x /tini

# Define the running user
USER php-user

# Application directory
WORKDIR "/var/www/app"

ENV PATH=/home/php-user/.composer/vendor/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin

# Define the entry point that tries to enable newrelic
ENTRYPOINT ["/tini", "--", "/scripts/start.sh"]

# As non daemon and single base image, it may be used as cli container
CMD ["/bin/bash"]
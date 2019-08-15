FROM centos:latest

RUN yum install gcc wget which make -y
RUN wget https://cache.ruby-lang.org/pub/ruby/2.6/ruby-2.6.3.tar.gz
RUN tar -xvzf ruby-2.6.3.tar.gz
RUN /ruby-2.6.3/configure
RUN make
RUN make install

RUN yum install curl zlib-devel curl-devel openssl-devel httpd-devel apr-level apr-util-devel mysql-devel ftp wget ImageMagick-devel gcc-c++ patch readline readline-devel zlib libyaml-devel libffi-devel make bzip2 autconf automake libtool bison subversion sqlite-devel elinks -y

RUN gpg --keyserver hkp://pool.sks-keyservers.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3 7D2BAF1CF37B13E2069D6956105BD0E739499BDB ; curl -sSL https://get.rvm.io | bash -s stable --ruby 


RUN export PATH=/usr/local/rvm/gems/ruby-2.6.3/bin:/usr/local/rvm/gems/ruby-2.6.3@global/bin:/usr/local/rvm/rubies/ruby-2.6.3/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/local/rvm/bin ; source /usr/local/rvm/scripts/rvm ; rvm install 2.3.6 --with-zlib-dir=/usr/local/Cellar/zlib/1.2.8 ; gem install rack -v '2.0.7' ;

RUN echo $PATH

ENV PATH=/usr/local/rvm/gems/ruby-2.6.3/bin:/usr/local/rvm/gems/ruby-2.6.3@global/bin:/usr/local/rvm/rubies/ruby-2.6.3/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/local/rvm/bin 
ENV rvm_bin_path=/usr/local/rvm/bin

ENV GEM_HOME=/usr/local/rvm/gems/ruby-2.6.3

#ENV TERM=xterm
#ENV IRBRC=/usr/local/rvm/rubies/ruby-2.6.3/.irbrc
ENV MY_RUBY_HOME=/usr/local/rvm/rubies/ruby-2.6.3

ENV rvm_path=/usr/local/rvm
ENV rvm_prefix=/usr/local

ENV GEM_PATH=/usr/local/rvm/gems/ruby-2.6.3:/usr/local/rvm/gems/ruby-2.6.3@global
#ENV install_flag=1
ENV RUBY_VERSION=ruby-2.6.3

ENV rvm_version=1.29.9
#ENV rvm_pretty_print_flag=auto

RUN gem install nokogiri -v '1.8'

RUN gem install rack -v '2.0.7'

RUN yum install sudo -y

RUN gem install passenger --conservative -v '5.2.1'
RUN env
#RUN passenger-install-apache2-module
#RUN yum install sudo -y
RUN passenger-install-apache2-module --auto


RUN  wget http://www.redmine.org/releases/redmine-3.3.3.tar.gz
RUN tar -xzvf redmine-3.3.3.tar.gz
RUN  cp -r redmine-3.3.3 /var/www/redmine

RUN gem install bundler -v "1.3.0"

# <not needed> cp /var/www/redmine/config/database.yml.example  /var/www/redmine/config/database.yml


# create database redmine_development character set utf8;

# create database redmine_test character set utf8;


# cat database.yml ("run the above two commands in the docker container")

# Remember to give the ip of mysql container here

RUN echo -e "production:\n  adapter: mysql2\n  database: redmine\n  host: 172.17.0.2\n  username: redmine\n  password: 'password'\n  encoding: utf8\n\ndevelopment:\n  adapter: mysql2\n  database: redmine_development\n  host: 172.17.0.2\n  username: redmine\n  password: 'password'\n  encoding: utf8\n\ntest:\n  adapter: mysql2\n  database: redmine_test\n  host: 172.17.0.2\n  username: redmine\n  password: 'password'\n  encoding: utf8" > /var/www/redmine/config/database.yml


RUN cp /var/www/redmine/config/configuration.yml.example /var/www/redmine/config/configuration.yml

RUN gem install bundler -v '1.3.6'
RUN gem install nokogiri -v '1.6.8'
RUN yum install mysql-devel -y

RUN gem install mysql2 -v 0.3.21
RUN gem install rails -v '4.2.7.1'
RUN gem install jquery-rails -v '3.1.4'
RUN gem install coderay -v '1.1.1'
RUN gem install request_store -v '1.0.5'
RUN gem install mime-types -v '3.0'
RUN gem install protected_attributes
RUN gem install actionpack-action_caching

RUN gem install actionpack-xml_parser
RUN gem install roadie-rails
RUN gem install mimemagic

RUN gem install i18n -v '0.7.0'
RUN gem install rbpdf -v '1.19.2'
RUN gem install net-ldap -v '0.12.0'
RUN gem install ruby-openid -v '2.3.0'

RUN gem install rack-openid

RUN gem install rmagick -v '2.14.0'
RUN gem install redcarpet -v '3.3.2'
RUN gem install rdoc -v '4.3'

RUN gem install yard
RUN gem install mocha


RUN gem install simplecov -v '0.9.1'

RUN gem install capybara
RUN gem install rdoc -v '6.1.1' 
RUN sed -i 's/gem "selenium-webdriver", "~> 2.53.4"/gem "selenium-webdriver","3.142.3"/g' /var/www/redmine/Gemfile
RUN sed -i 's/gem "nokogiri", "~> 1.6.8"/gem "nokogiri","1.8"/g' /var/www/redmine/Gemfile

RUN gem install selenium-webdriver -v '3.142.3'

RUN rake -f /var/www/redmine/Rakefile generate_secret_token

RUN RAILS_ENV=production rake db:migrate

RUN RAILS_ENV=production rake redmine:load_default_data

RUN chown -R apache:apache /var/www/redmine

RUN chmod 755 /var/www/redmine/files/
RUN chmod 755 /var/www/redmine/log
RUN chmod 755 /var/www/redmine/tmp
RUN chmod 755 /var/www/redmine/public
RUN chmod 755 /var/www/redmine/plugins

# remember the servername must be redmine.example.com

RUN echo -e "LoadModule passenger_module /usr/local/rvm/gems/ruby-2.6.3/gems/passenger-6.0.2/buildout/apache2/mod_passenger.so\n   <IfModule mod_passenger.c>\n     PassengerRoot /usr/local/rvm/gems/ruby-2.6.3/gems/passenger-6.0.2\n     PassengerDefaultRuby /usr/local/rvm/gems/ruby-2.6.3/wrappers/ruby\n   </IfModule>\n\n\n<VirtualHost *:80>\n    ServerName redmine.example.com\n    DocumentRoot '/var/www/redmine/public'\n    <Directory '/var/www/redmine/public'>\n      Allow from all\n      Options -MultiViews\n    </Directory>\n</VirtualHost>" > /etc/httpd/conf.d/redmine.conf

CMD /usr/bin/httpd -DFOREGROUND


 # yum install sudoers -y

 # vi /etc/sudoers apache  ALL=(ALL)       NOPASSWD: ALL

# mailserver #

master branch: [![Build Status](https://secure.travis-ci.org/saschajulien/mailserver.png?branch=master)](http://travis-ci.org/saschajulien/mailserver)

This Module provides a mailserver with postfix as SMTP-Server and dovecot as IMAP Server with virtual configuration stored in a postgres database including virus scanning with clamav. Patches and Feature Requests are welcome and can be handed in at [Github](http://github.com/saschajulien/). 

Dependencies:
============

- postfix
- dovecot
- amavis
- clamav
- postgresql


Prerequisites:
============
You need to install augeas-lenses and libruby-augeas beforehand, otherwise the installation will just fail because most of the configuration is done using augeas.


Simplest Configuration:
=============

    class { 'mailserver':
      ssl_key_file  =>'/etc/ssl/private/ssl-cert-snakeoil.key',
      ssl_cert_file =>'/etc/ssl/certs/ssl-cert-snakeoil.pem',
      dbpassword    =>'password',
      dbuser        =>'username',
      domains	    => ["$::fqdn"],
      users         => { "root@${::fqdn}" => { 
  	                 password=> 'wt3T3FHETdggQ', quota => '100000' }
                       }
    }

Configuration with forwards

    class { 'mailserver':
      ssl_key_file  =>'/etc/ssl/private/ssl-cert-snakeoil.key',
      ssl_cert_file =>'/etc/ssl/certs/ssl-cert-snakeoil.pem',
      dbpassword    =>'password',
      dbuser        =>'username',
      domains	    => ["$::fqdn"],
      users         => { "root@${::fqdn}" => { 
  	                 password=> 'wt3T3FHETdggQ', quota => '100000' }
                       },
      forwards      => { 
                         "postmaster@${::fqdn}" => { destination => "root@${::fqdn}" },
                         "admin@${::fqdn}"      => { destination => ["root@${::fqdn}","post@${::fqdn}"] }
                       }
   }

Passwords have to be encrypted beforehand, use the following: 
    perl -e 'print crypt('password','salt')."\n"'

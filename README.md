# puppetworld

```

Installation
============
apt-get install puppetmaster
apt-get install puppet

Configuration File
==================
Agent
======
$cat /etc/puppet/puppet.conf 

[main]
server=ceph1
logdir=/var/log/puppet
vardir=/var/lib/puppet
ssldir=/var/lib/puppet/ssl
rundir=/var/run/puppet
factpath=$vardir/lib/facter
templatedir=$confdir/templates
runinterval = 1h

[master]
# These are needed when the puppetmaster is run by passenger
# and can safely be removed if webrick is used.
ssl_client_header = SSL_CLIENT_S_DN 
ssl_client_verify_header = SSL_CLIENT_VERIFY

Master
=====
$cat /etc/puppet/puppet.conf 

[main]
certname = ceph1
server = ceph1
environment = production
runinterval = 1h
strict_variables = true

[master]
dns_alt_names = ceph1,ceph1.local


Building puppet module
======================
If starting from scratch (To build basic template for you)
puppet module generate puppet-firstmodule

cd puppet-firstmodule

edit manifests/init.pp

node '<hostname-of-your-machine>' {
        file {"/tmp/myfirstmodule":
                content => "Hello World from myfirstmodule",
        }
}

puppet module build puppet-firstmodule or puppet module build (if already in puppet-firstmodule directory)

As root install puppet module
puppet module install puppet-firstmodule-0.1.0.tar.gz

Verify if module is installed or not
puppet module list

Testing
=======
In debug mode
puppet master --no-daemonize --debug --verbose

Start Puppet Master (Once everthing is verified)
/etc/init.d/puppetmaster start

Verify for creation of /tmp/myfirstmodule on master
puppet apply manifest/init.pp

Testing Agent
Include module into to manifest file

$cat /etc/puppet/manifests/site.pp
import 'firstmodule'               =====> your module name

puppet agent -t --debug --verbose    ==> It will need certificate signup

On master
sudo puppet cert --sign <agenthostname>

On Agent (Repeat); 
puppet agent -t --debug --verbose   or puppet agent --test

Verify for creation of /tmp/myfirstmodule on agent

Delete /tmp/myfirstmodule and it should get recreated after specified time interval 

Change to shorten agent run interval

runinterval = 1h


Cleanup
=======
To uninstall install module

puppet module uninstall puppet-firstmodule
puppet module uninstall puppetlabs-stdlib

sudo apt-get remove --auto-remove puppetmaster
sudo apt-get remove --auto-remove puppet

Purge your config data too....

sudo apt-get purge --auto-remove puppetmaster
sudo apt-get purge --auto-remove puppet


Issues
=====
1) Could not request certificate: getaddrinfo: Name or service not known

$ puppet agent --configprint server
puppet

Update /etc/puppet/puppet.conf 

cat /etc/puppet/puppet.conf 
[main]
certname = <agenthostname>
server = <masterhostname>
environment = production
runinterval = 1h


$ puppet agent --configprint server
<masterhostname>

2) err: Could not retrieve catalog from remote server: SSL_connect returned=1 errno=0 state=SSLv3
master:
puppet cert clean <NODE NAME>

agent:
rm -r $(puppet agent --configprint ssldir)
puppet agent --test


2) Could not find certificate request
puppet node clean <agenthostname>     //on master

rm -rf /var/lib/puppet/ssl   //on agent

restart master
puppet master --no-daemonize --debug --verbose

restart agent
puppet agent --server <masterhostname> --no-daemonize --verbose

on master
puppet cert list
 >>> should have agent MD5
puppet cert --sign <agenthostname>    (sign the certificate)

puppet.conf missing on master (recreate one)
puppet master --genconfig > /etc/puppet/puppet.conf


puppet apply works on master but fails for agent (catalog not compiled)

3) Warning: Missing dependency 'puppetlabs-stdlib':
  'puppet-firstmodule' (v0.1.0) requires 'puppetlabs-stdlib' (>= 1.0.0)
sed -i -e 's|puppetlabs-stdlib|puppetlabs/stdlib|' metadata.json

4) Could not create PID file: /var/lib/puppet/run/master.pid
   Either puppet instance is running or someother application is using it.
	lsof -i :8140    >>> verify
   Kill the process service puppetmaster stop

```

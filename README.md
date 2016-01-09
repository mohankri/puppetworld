# puppetworld

```

Installation
============
apt-get install puppetmaster
apt-get install puppet

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
puppet agent -t --debug --verbose


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

[agent]
server=master.hostname

$ puppet agent --configprint server
master.hostname



2) Could not find certificate request
puppet node clean <agenthostname>     //on master

rm -rf /var/lib/puppet/ssl   //on agent

restart master
puppet master --no-daemonize --debug --verbose

restart agent
puppet agent --server masterhostname --no-daemonize --verbose

on master
puppet cert list
 >>> should have agent MD5
puppet cert --sign agenthostname   >>> sign the certificate

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

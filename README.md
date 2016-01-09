# puppetworld

```
Server
apt-get install puppetmaster

Client
apt-get install puppet

If starting from scratch (To build basic template for you)
puppet module generate puppet-firstmodule

cd puppet-firstmodule

Edit new filesite.pp with import 'init.pp'

Edit manifests/init.pp

node '<hostname-of-your-machine>' {
        file {"/tmp/myfirstmodule":
                content => "Hello World from myfirstmodule",
        }
}

Start Puppet Master
/etc/init.d/puppetmaster start

Manual Testing of Hello World
puppet apply manifest/site.pp

Connecting Puppet Agent to Puppet Master


Building Module
===============
puppet module build puppet-firstmodule

As root install puppet module
puppet module install puppet-firstmodule-0.1.0.tar.gz

List installed Puppet
puppet module list

Uninstall puppet module
puppet module uninstall puppet-firstmodule
puppet module uninstall puppetlabs-stdlib

Issues
=====
1) Could not request certificate: getaddrinfo: Name or service not known

$ puppet agent --configprint server
puppet

[agent]
server=master.hostname

$ puppet agent --configprint server
master.hostname

Verified
puppet agent -t --debug --verbose


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
```

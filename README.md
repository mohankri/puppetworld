# puppetworld
<p>
Server
apt-get install puppetmaster

Client
apt-get install puppet

If starting from scratch (To build basic template for you)
puppet module generate puppet-firstmodule

cd puppet-firstmodule

Edit new filesite.pp with import 'init.pp'

Edit manifests/init.pp

node "<hostname-of-your-machine>" {
        file {"/tmp/myfirstmodule":
                content => "Hello World from myfirstmodule",
        }
}

Start Puppet Master
/etc/init.d/puppetmaster start

Manual Testing of Hello World
puppet apply manifest/site.pp
</p>


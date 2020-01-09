# Puppet-Intermediate-CA-2-Active-Directory

```
puppet resource service puppet ensure=stopped
puppet resource service pe-puppetserver ensure=stopped
puppet resource service pe-activemq ensure=stopped
puppet resource service mcollective ensure=stopped
puppet resource service pe-puppetdb ensure=stopped
puppet resource service pe-postgresql ensure=stopped
puppet resource service pe-console-services ensure=stopped
puppet resource service pe-nginx ensure=stopped
puppet resource service pe-orchestration-services ensure=stopped
puppet resource service pxp-agent ensure=stopped

tar -zcvf /root/ssl.$(date +%Y%m%d-%H%M).backup.tar.gz /etc/puppetlabs/puppet/ssl

find /etc/puppetlabs/puppet/ssl/ -type f -delete

cd /var/tmp/ca/
cp ca_crt.pem /etc/puppetlabs/puppet/ssl/ca/ca_crt.pem
cp ca_key.pem /etc/puppetlabs/puppet/ssl/ca/ca_key.pem
cp root_crt.pem /etc/puppetlabs/puppet/ssl/ca/root_crt.pem

chmod 600 /etc/puppetlabs/puppet/ssl/ca/ca_crt.pem /etc/puppetlabs/puppet/ssl/ca/ca_key.pem /etc/puppetlabs/puppet/ssl/ca/root_crt.pem
cd /etc/puppetlabs/puppet/ssl/ca
cat ca_crt.pem root_crt.pem > ../certs/ca.pem

puppet cert generate fakehost
puppet cert clean fakehost
puppet cert generate $(hostname -f) --dns_alt_names=$(hostname -f),$(hostname),puppet
puppet resource service pe-puppetserver ensure=running
puppet config set --section main certificate_revocation false
puppet infra configure
puppet agent -t
```

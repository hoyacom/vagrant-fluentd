# fluentd-vagrant

Project to deploy a Redhat based fluentd collector, from rsyslog, running on
multiple different Linux and Windows hosts.

## Deploy

```shell
git clone https://github.com/apolloclark/vagrant-fluentd

# configure Redhat subscription variables
REDHAT_USER=''
REDHAT_PASS=''

# launch master collector
cd ./vagrant-fluentd/fluentd-master-rhel7.4
vagrant up
```


## Logs
```shell

# fluentd

## config
nano /etc/td-agent/td-agent.conf

## verify config
td-agent --dry-run -c /etc/td-agent/td-agent.conf

## check fluentd / td-agent status

systemctl status td-agent.service

## restart fluentd / td-agent
systemctl restart td-agent.service

## logs
nano /var/log/td-agent/td-agent.log
watch -n 1 "tail -n 48 /var/log/td-agent/td-agent.log"

## check if selinux is blocking fluentd
grep -F "td-agent" /var/log/audit/audit.log | grep -F "success=no"

## configure selinux

semanage port -a -t syslogd_port_t -p tcp 5140
semanage port -l | grep -F "syslogd_port_t" | grep 5140



# rsyslogd

## check for errors
cat /var/log/messages | grep rsyslogd

## check if SELinux is blocking rsyslog
grep -F "/usr/sbin/rsyslogd" /var/log/audit/audit.log | grep -F "success=no"

## test logging
logger testmessage5
grep -F "testmessage5" /var/log/messages
sleep 30s
grep -F "testmessage5" /var/log/td-agent/td-agent.log
```
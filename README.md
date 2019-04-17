Role for installing tick stack (- chronograf + grafana), using docker swarm


The ansible controller needs Jinja2 to use the templates

The target needs influxdb python package(installed with pip in the role)

In the files a file called snmpHosts.csv is used to retrieve the ip/community/model to monitor from snmp, and pingHosts.csv has the ip to perform a ping checkup. Finally a load/tasks/ folder includes the tick scripts

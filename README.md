Role for installing tick stack (- chronograf + grafana), using docker swarm


The ansible controller needs Jinja2 to use the templates

The target needs influxdb python package(installed with pip in the role)

In the files a file called snmpHosts.csv is used to retrieve the ip/community/model to monitor from snmp, and pingHosts.csv has the ip to perform a ping checkup. Finally a load/tasks/ folder includes the tick scripts

It adds a grafana dashboard showing basic snmp data(interfaces/uptime).

*El script lee snmpHosts.csv y a esos agentes le monitorea datos basicos de networking(solo mira las interfaces, no se preocupa del modelo), de pingHost.csv lee las ip para el modulo ping. De la carpeta files/load/tasks levanta los tick scripts que kapacitor va a correr


Para correr el script hay que generar un archivo de inventario
Ej.:
inventory.txt
####

target1 ansible_host=192.168.11.117 ansible_connection=ssh ansible_user=root ansible_ssh_pass=osboxes.org
[PC]
target1
[PC:vars]
 influxIp= http://influxdb:8086
#Ip en formato influxdb:8086
 influxIp2= 192.168.11.117
#Ip del host donde instalo
 influxDatabase= test
#Nombre de la base donde se van a mandar los datos
 telegraf_agent_version= 1.9.4 
#Version de telegraf a usar

###

Y un archivo de Ansible
Ej.:

swarmsetup.yml
####

- name: instalacion
  hosts: all
  gather_facts: true

  vars:
    docker_users:
      - "{{ ansible_env.USER }}"
    var_home: "{{ ansible_env.HOME }}"
    var_user: "{{ ansible_env.USER }}"
    ANSIBLE_DEBUG: 1

  roles:
    - { role: tickStack, become: true }

###

Quedarian los archivos inventory.txt swarmsetup.yml y roles/tickStack
Y corriendo:
ansible-playbook -i inventory.txt swarmsetup.yml
Corre el script instalando todo

La parte de grafana.yml, cambia la pass default de grafana, agrega la base de influx y crea un dashboard que muestra la parte basica de snmp.


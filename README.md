Role for installing tick stack (- chronograf + grafana), using docker swarm

REQUIREMENTS:

    - Target has to have docker/pip (01Startup.yml && 02Config.yml)
    - The ansible controller needs Jinja2 to use the templates
    - The target needs influxdb python package(installed with pip in the role)

In the files a file called snmpHosts.csv is used to retrieve the ip/community/model to monitor from snmp, and pingHosts.csv has the ip to perform a ping checkup.

 Finally a load/ folder includes the tick scripts
 It has a basic template to add scripts.

It reads a csv file with the grafana dashboards to upload(needs improvement).

*El script lee snmpHosts.csv y a esos agentes le monitorea datos basicos de networking(solo mira las interfaces, no se preocupa del modelo), de pingHost.csv lee las ip para el modulo ping. De la carpeta files/load/tasks levanta los tick scripts que kapacitor va a correr


Para agregar los scripts, con el template por cada yaml en files/tasks genera un script con los parametros que se definan en el yaml. (falta que ansible genere los yaml a partir del inventario)



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

La parte de grafana.yml, cambia la pass default de grafana, agrega la base de influx, y despues mira los dashboards que estan en el csv(hay que cambiar la manera de elegirlos, para que suba todos directos o sea un comando mas facil en ves de un csv) y agrega esos dashboards a grafana.



# Para usar proxmox con ansible Seguir los pasos

"Las palabras entre asterisco hara referencia alos comandos que usaremos para referenciarlo con la informacion dentro de los archivos que vamos creando, por lo cual no pongais * a los comandos o dentro de los archivos."

1º Descargar sshpass

2º Para poder identificar las maquinas y añadir valores crear el fichero inventory, introducir datos por ejemplo.
---
[Proxmox*]
192.168.1.20
192.168.1.21
192.168.1.22

[all:vars]
ansible_user=root
token_id=user
token_secret=16b0e28f-9e13-450b-8fd1-8b1888252856
ansible_ssh_private_key_file=~/.ssh/id_rsa
ansible_connection=ssh
ansible_ssh_common_args='-o StrictHostKeyChecking=no'

---

3º Crear el archivo en tu proyecto llamado 'ansible.cfg' dentro para parametrizar configuraciones de ansible, ejemplo:

[default]
interpreter_python=auto_silent
host_key_cheking=False

4º Para probar que tienes conexion con las maquinas puedes hacer un ping con el siguiente comando: 

$ ansible proxmox* -i inventory -m ping --user=root -k

Este nos pedira la contraseña de la maquina master en proxmox

5º Creamos el primer playbook para poder configurar la clave ssh, esta se llamara pve_onboard.yml

Para poder llenar los parametros de la key ssh, tenemos que realizar el comando 

$ ssh-keygen -t ed25519 -C "tu-correo-electronico"

6º Creamos una carpeta llamado files, que contendra un fichero llamado sudoer_ansible.

7º Para que surja los cambios escribir el siguiente comando:

$ ansible-playbook pve_onboard.yml -i invemtory --user=root -k

Nos pedira de nueve la contraseña, pero despues solo usaremos la clave privada que contiene nuestra maquina ansible.

8º Podemos probar un ping con el siguiente comando:

$ ansible proxmox -m ping -i invetory --user=ansible --private-key ~/.ssh/"vuestro-archivo-con-la-clave-privada"

Para conseguir el token desde proxmox seguir el video este: 

https://www.youtube.com/watch?v=4G9d5COhOvI

9º Lo que sigue es instalar proxmoxer en nuestro servidor proxmox. Por los que creamos otro playbook "pve_install_proxmoxer.yml"

Y usamos el comando siguiente para iniciarlo:

$ ansible-playbook pve_install_proxmoxer.yml -i invetory --user=ansible --private-key ~/.ssh/"vuestro-archivo-con-la-clave-privada"

10ª Por ultimo para crear un VM basica crearemos el siguiente fichero: "pve_create_vm.yml"

Usaremos el comando de antes para iniciarlo:

$ ansible-playbook pve_create_vm.yml -i invetory --user=ansible --private-key ~/.ssh/"vuestro-archivo-con-la-clave-privada"
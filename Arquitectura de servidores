# Arquitectura de servidores

***Recuerda que deberas asignar un caso en caso de que no pueda ingresar al lxd init, debera configurar con "sudo lxd init"***.

El objetivo de esta asignatura es comprender y llevar a cabo prácticas que estén relacionadas a la arquitectura de servidores.

## Fundamentos de servidores y visualización.

1. Servidores.
2. Virtualización.
3. Contenedores.

---

##### 1. Un servidor es una computadora que proporciona servicios a otras computadoras (llamadas clientes). Los servicios principales que pueden proporcionar son: 
- Acceso a paginas web.
- Bases de datos. 
- Correos electrónicos.

Existen diferentes maneras de sacarle provecho a estos servidores ademas de que estos pueden ser tanto físicos como virtuales.

---

##### 2. La virtualización de maquinas no es mas que la simulación de todo un sistema operativo y el hadware en nuestra maquina principal (Anfitrión).

Esta se encarga de crear un entorno completamente nuevo sobre nuestro anfitrión, este tiene algunas desventajas:

- El tiempo de espera puede ser mayor al inicializar.
- Es mas pesado y puede volverse lento según los componentes del hardware de nuestra maquina principal.

---

##### 3. Los contenedores se dividen en dos (LXC y LXD):

- LXC (Linux Containers): Estos se encargan de ejecutar sistemas operativos pero compartiendo el núcleo del sistema operativo principal, estos son mas ligeros ya que solo trabajan de una manera aislada y con contenedores de manera individual.

- LXD (Linux Containers Deamon): Esto es una mejora al tipo LXC ya que contiene funcionalidades mas avanzadas y permite gestionar varios contenedores, también este tipo contiene herramientas mas avanzadas para facilitar su uso.

Cabe mencionar que al utilizar LXD estas usando internamente el LXC, debido a que es una administración avanzada de LXC, al momento de instalar LXD también se instalará el LXC.

## Instalaciones y comandos básicos.

Usaremos el LXD ya que también se usa internamente el LXC y es el que mas nos facilita el gestionar varios contenedores.

- Instalación de LXD.

        sudo snap install lxd
    
- Agregar tu usuario al grupo LXD para no escribir sudo cada que quieras ejecutar comandos.
        
        sudo usermod -aG lxd $USER
        
    ***Es importante cerrar sesión para que los cambios se apliquen.***
       
- Configurar el LXD.

        lxd init
        
Después de hacer esto nos dirigirá a otra sección de la terminal, aquí se responderán a algunas preguntas.
    
    "Would you like to use LXD clustering?" → No.
    "Do you want to configure a new storage pool?" → Sí.
    "Would you like to connect LXD to an existing network bridge?" → No.
    "Would you like LXD to be available over the network?" → No.

Ahora ya esta listo para usarse el LXD.

## Contenedores

Aquí se veran los comandos sencillos para los contenedores.

- Crear un contenedor nuevo.
        
        lxc launch ubuntu:22.04 contenedor-1
        
    Ajusta la versión de ubuntu al sistema operativo en el que estés trabajando y si quieres sustituye el nombre del contenedor.
      
- Ver una lista de contenedores en ejecución.

        lxc list
 
- Ingresar al contenedor y usarlo como si fuera servidor.

        lxc exec {nombre-del-contenedor} --bash
        
- Salir de un contenedor.

        exit

- Detener un contenedor en ejecución.

        lxc stop {nombre-del-contenedor}
        
- Iniciar un contenedor detenido.

        lxc star {nombre-del-contenedor}

- Para eliminar un contenedor (antes debes detenerlo si esta en ejecución).

        lxc delete {nombre-del-contenedor}

### Configuración de redes de contenedores

Los comandos a continuación son para poder acceder a la red a través de perfiles, estos perfiles después de crearlos y editarlos podremos aplicar el perfil al contenedor.

- Acceder a la lista de las redes que tienen LXD y verificar si el contenedor tiene acceso a la red.

        lxc network list
        
    Debería de haber una respuesta por defecto del contenedor ***"lxdbr0"*** (si no aparece al final hay un apartado donde se configura el lxd init de manera manual).

- Crear un perfil de IPvlan.

        lxc profile create ipvlan
        
    Puedes cambiar al nombre que tu quieras pero como estamos creando un perfil ipvlan, por eso lo nombre así, pero puedes poner el que quieras.

- Editar un perfil IPvlan

        lxc profile edit {Nombre-del-perfil}
        
Ahora se de despliegara una nueva seccion en la terminal la cual cambiaras a tus necesidades.

    config:
      user.network-config: |
        #cloud-config
        version: 2
        ethernets:
          {nombre_interfaz}:
            addresses:
              - {ip_contenedor}/{mascara_red}
            dhcp4: {dhcp4}
            dhcp6: {dhcp6}
            nameservers:
              addresses: [{dns1}, {dns2}]
            routes:
              - to: 0.0.0.0/0
                via: {gateway}
                on-link: true
    
    description: {descripcion_perfil}
    
    devices:
      {nombre_interfaz}:
        ipv4.address: {ip_contenedor}
        nictype: ipvlan
        parent: {interfaz_host}
        type: nic
        name: {nombre_interfaz}
    
    used_by:
      - /1.0/instances/{nombre_instancia}
            
Deberás sustituir lo que necesites para que tu perfil funcione, enseguida te mostrare una tabla donde podrás visualizar los parámetros personalizables con ejemplos de como llenar ese campo.

#### Parámetros personalizables:

| **Variable**           | **Descripción**                               | **Ejemplo**                  |
|------------------------|-----------------------------------------------|------------------------------|
| `{nombre_interfaz}`    | Nombre de la interfaz dentro del contenedor   | `eth0`                       |
| `{ip_contenedor}`      | IP que tendrá el contenedor                   | `192.168.1.100`              |
| `{mascara_red}`        | Máscara de red en formato CIDR                | `24`                         |
| `{dhcp4}`              | Habilitar o deshabilitar DHCPv4 (`yes/no`)    | `no`                         |
| `{dhcp6}`              | Habilitar o deshabilitar DHCPv6 (`yes/no`)    | `no`                         |
| `{dns1}, {dns2}`       | Servidores DNS a usar                         | `8.8.8.8, 1.1.1.1`           |
| `{gateway}`            | Dirección del gateway                         | `192.168.1.1`                |
| `{descripcion_perfil}` | Nombre o descripción del perfil               | `Perfil de red IPVLAN`       |
| `{interfaz_host}`      | Interfaz del host que usará el contenedor     | `wlo1` o `enp3s0`            |
| `{nombre_instancia}`   | Nombre del contenedor LXD                     | `mi_contenedor`              |

Al sustituir los parámetros un ejemplo practico seria el siguiente:

    config:
      user.network-config: |
        #cloud-config
        version: 2
        ethernets:
          eth0:
            addresses:
              - 192.168.68.200/32
            dhcp4: no
            dhcp6: no
            nameservers:
              addresses: [8.8.8.8, 1.1.1.1]
            routes:
              - to: 0.0.0.0/0
                via: 169.254.0.1
                on-link: true
    
    description: ipvlan LXD profile
    
    devices:
      eth0:
        ipv4.address: 192.168.68.210
        nictype: ipvlan
        parent: wlo1
        type: nic
        name: ipvlan
    
    used_by:
      - /1.0/instances/myipvlan

-   Ahora aplicamos el perfil a un contenedor.

        lxc launch ubuntu:22.04 {nombre-del-contenedor} --profile default --profile {nombre-del-perfil}

### Ahora ejecutaremos aplicaciones gráficas dentro de los contenedores.

Crearemos un nuevo perfil GIU, esto para poder correr una aplicación gráfica dentro de un contenedor.

- Creamos un nuevo perfil gui.

        lxc profile create gui
        
- Ahora editamos el perfil.

        lxc profile edit gui
        
Ahora se desplegara una nueva sección en la terminal en la cual deberás añadir el siguiente contenido (copiar y pegar), el cual habilita la interfaz gráfica de usuario dentro del contenedor.

    config:
      environment.DISPLAY: :0
      raw.idmap: both 1000 1000
      user.user-data: |
        #cloud-config
        runcmd:
          - 'sed -i "s/; enable-shm = yes/enable-shm = no/g" /etc/pulse/client.conf'
          - 'echo export PULSE_SERVER=unix:/tmp/.pulse-native | tee --append /home/ubuntu/.profile'
        packages:
          - x11-apps
          - mesa-utils
          - pulseaudio
    description: GUI LXD profile
    devices:
      PASocket:
        path: /tmp/.pulse-native
        source: /run/user/1000/pulse/native
        type: disk
      X0:
        path: /tmp/.X11-unix/X0
        source: /tmp/.X11-unix/X0
        type: disk
      mygpu:
        type: gpu


- Ahora crearemos un contenedor y aplicaremos el perfil default (es el que viene predeterminado de LXD y generalmente configura red y otros ajustes básicos) y gui creado anteriormente, esto para ahorrarnos lineas de código en la terminal.

        lxc launch ubuntu:22.04 {nombre-del-contedor} --profile default --profile {nombre-del-perfil-gui}
        
- Ahora accederemos al contenedor

    lxc exec {nombre-del-contedor} -- bash
        
- Actualizamos la liste de paquetes.

        apt update

Lo que sigue a continuación la ejecución de una aplicación gráfica dentro del contenedor.

- Primero descargamos el paquete, en este caso de Google Chrome.

        wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb

- Ahora lo que haces sera instalarlo.  
  
        apt install -y ./google-chrome-stable_current_amd64.deb

- Por ultimo lo ejecutamos.

        google-chrome-stable

    Nota: En algunos entornos de contenedores, puede ser necesario ejecutar Chrome con la opción --no-sandbox para evitar problemas de permisos. Por ejemplo:

        google-chrome-stable --no-sandbox
        








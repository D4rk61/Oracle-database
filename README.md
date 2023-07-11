# Instalacion definitiva de ORACLE DATABASE EN **ORACLE LINUX**


###### Antes de empezar: Me disculpo si mi explicacion no es la mas clara, es de las primeras veces que instalo ORacle-Database; cualquier duda que tengan pueden hacer uso de ChatGPT copiando y pegando la guia que aqui he escrito y si les llega a dar algun error pegar el "output" (la salida del error) en chatGPT y decir que, que pueden hacer; los archivos estan dentro de carpeta **recursos**

### Pre-instalacion

Los archivos .rpm se encuentran en la carpeta **recursos**; los archivos por cuestiones de espacio los subi a google drive, solo es cuestion de entrar y descargarlos :D


1. Instalaremos la ISO de oracle linux, disponible en este repositorio,
   recomiendo instalar la version **Oracle 8** intente user la version 9 y no
   funcionaron algunas cosas.


2. Configuraremos el server de primera instancia normal, solo algunas
   recomendaciones, **en virtualbox activamos la red via puente**
   ![red](/img/Captura%20de%20pantalla_2023-07-10_01-58-27.png)

**CONSEJOS:** 
- Recomiendo ponerle a la maquina virtual un minimo de 2.5 de RAM
- Recomiendo poner mucho disco duro a la maquina virtual; ya que aqui las bases de datos ocupan mucho disco

### Configuracion de entorno 

1. Tener conexion ssh con el servidor oracle, esto lo recomiendo para hacer mas facil la manipulacion del servidor ejecutando lo siguiente:


```bash
# en el servidor
sudo systemctl status sshd.service
sudo systemctl start --now sshd.service
sudo systemctl enable sshd.service
```

0. Para conectarnos al servidor remoto lo haremos asi: 

```bash

ssh {usuario}@{hostname}

# usuario => usuario del servidor puede ser root
# hostname => IP del servidor, normalmente es algo como: 192.168.0.8

```

2. Ejecutar este comando: **(Supliendo las dependencias)**


```bash
sudo dnf install -y bc flex libnsl libnsl.i686 libaio libaio.i686 numactl.x86_64 numactl-libs.x86_64 unixODBC.x86_64 bind-utils binutils compat-openssl10 glibc-devel ksh make nfs-utils policycoreutils-python-utils sysstat xorg-x11-utils xorg-x11-xauth

```

3. Luego de suplir las dependencias, **Instalaremos el "pre-install"**


```bash
sudo rpm -ivh oracle-database-preinstall-21c-1.0-1.el8.x86_64.rpm

```

### Instalacion del software
- **. Mover los archivos de nuestra maquina host a una maquina virtual:**

Esta es la forma mas facil de mover archivos de nuestra maquina host a una maquina virtual; que seria usando el protocolo **ssh**

```bash
scp {archivo} {usuario}@{host}:{ruta}

#  ejemplo:
scp oracle-database-ee-21c-1.0-1.ol8.x86_64.rpm oracle@192.168.0.5:~/Descargas
```

Con esto podemos mover los archivos que descargamos desde el drive **(El archivo de la base de datos, esta en la carpeta "recursos")** a una maquina virtual o inclusive a servidores remotos


4. **Instalacion de la Oracle-database**


```bash
sudo rpm -ivh oracle-database-ee-21c-1.0-1.ol8.x86_64.rpm

```

5. **Configuracion de Oracle Database con el Script**

Si todo va bien, nos aparecera algo como esto: 

![texto](/img/Captura%20de%20pantalla_2023-07-10_02-08-38.png)

**!Advertencia:** Este proceso puede demorar algo de tiempo, tengan paciencia

```bash

# Antes de ejecutar el script

sudo yum install -y tmux
tmux

# esto nos ayudara a tener diferentes instancias de la terminal de nuestro server

CTRL+B+%

# Ejecutaremos esto en una terminal
sudo su
/etc/init.d/oracledb_ORCLCDB-21c configure

# Paralelo a esto instalaremos gnome para administrar de menor manerar a oracle database

sudo yum install -y gdm && yum install -y @gnome-desktop

sudo systemctl start --now gdm && sudo systemctl enable gdm && sudo systemctl set-default graphical.target
```

6. Configuracion del usuario **oracle**


```bash
sudo passwd oracle

# Aqui puedes asignar una contraseña que recuerdes
```


7. Reiniciamos la pc e iniciamos session con el usuario oracle y la respectiva contraseña que le pusimos


![conexion](/img/sshBuena.png)

<br>

8. Configuracion de las variables de entorno **!Importante**; este paso decidi automatizarlo para que a futuro no se quiebren la cabeza.


```bash
# Configuracion de las variables de entorno, porfavor seguir el orden

# 1. Ejecutar el binario dentro de dbhome_1/bin

source /opt/oracle/product/21c/dbhome_1/bin/oraenv 

[oracle@localhost ~]$ source /opt/oracle/product/21c/
dbhome_1/bin/oraenv 
ORACLE_SID = [oracle] ?   [Enter]
ORACLE_HOME = [/home/oracle] ?  /opt/oracle/product/21c/dbhome_1


# en donde dice ORACLE_HOME colocar hasta => dbhome_1

```

Por si hubo alguien que se perdio lo que tenemos que hacer es movernos al directorio `/dbhome_1/bin` y ejecutar el binario `oraenv` posterior a esto ponemos la ruta donde esta el **$ORACLE_HOME** 


![oraenv](/img/configuracionDeOraEnv.png)


9. Agregar automatizacion con **.bashrc**, antes de cerrar tu pc, en este paso agrega esto:


```bash
cd /home/oracle
vim .bashrc

# Agregar esto:

export ORACLE_BASE=/opt/oracle
export ORACLE_HOME=/opt/oracle/product/21c/dbhome_1
alias dbenv="source /usr/local/bin/oraenv"

```


Con esto ya no tenemos que realizar la configuracion de las variables de entorno cada vez que iniciemos session

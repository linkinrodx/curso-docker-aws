# Curso kubernetes y docker en aws desde cero

link del [curso](https://www.udemy.com/course/kubernetes-y-docker-en-aws-desde-cero/)

## Primer Paso: Configuracion de Entorno

lo primero que necessitamos es crear el entorno de desarrollo, para eso instalamos [ubuntu](https://ubuntu.com/#download) en una maquina virtual (ej: [virtualbox](https://www.virtualbox.org/wiki/Downloads))

si se tienen problemas durante la instalacion revisar el siguiente [link](https://www.youtube.com/watch?v=uV5boDESAe0)

Una vez instalado, agregamos ciertas caracteristicas como los guest aditions.

Dentro de Ubuntu, abrimos una ventana de comandos y agregamos los siguientes comandos:

```
sudo apt get
sudo apt upgrade
```

Luego verificamos si la maquina virtual tiene disponible la virtualizacion con el siguiente comando


```
cat /proc/cpuinfo| egrep "vmx|svm"
```

Ahora vamos a instalar docker, buscamos en google [install docker ce](https://docs.docker.com/install/linux/docker-ce/ubuntu/), ahi nos indica que comandos lanzar

```
$ sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common
```

luego agregamos una clave gpg de docker 

```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```

añadimos el repositorio oficial de docker

```
sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
```

Ahora actualizamos los paquetes con este nuevo repo

```
sudo apt-get update
```

y Ahora instalamos docker

```
sudo apt-get install docker-ce docker-ce-cli containerd.io
```

probamos que se haya instalado correctamente con

```
docker ps
```

Para tener acceso a nuestro usuario a docker, lanzamos el siguiente comando
Añadir un nuevo grupo al usuario

```
usermod -a -G docker rodrigo
```

reiniciamos nuestra maquina y probamos que podemos consultar docker desde la raiz

Ahora Instalamos [kubectl](https://kubernetes.io/es/docs/tasks/tools/install-kubectl/)

usarmos la instalacion de binario curl

```
curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl
```

nos descarga el binario en la raiz

probamos ejecutarlo

```
./kubectl
```

pero nos da denegacion de permiso, debemos acceder al permiso

```
chmod +x kubectl
```

ahora probamos y vemos la configuracion, tambien podemos ver la version

```
./kubectl
./kubectl version
```

movemos el binario la carpeta de binarios

```
mv kubectl /usr/local/bin/
```

Ahora instalamos [minikube](https://kubernetes.io/es/docs/tasks/tools/install-minikube/)

ejecutamos el siguiente comando

```
curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64 \
  && chmod +x minikube
```

movemos el binario a la carpeta de binarios

```
sudo mv minikube /usr/local/bin
```

probamos que todo este correcto

```
minikube
minikube version
```

Ahora instalamos [dockercompose](https://docs.docker.com/compose/install/)

```
sudo curl -L "https://github.com/docker/compose/releases/download/1.25.3/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```

damos permisos de acceso

```
sudo chmod +x docker-compose
```

verificamos el acceso

```
docker-compose
docker-compose version
```

Y por ultimo instalamos [awscli](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2-linux-mac.html#cliv2-linux-mac-install)

descargamos

```
curl "https://d1vvhvl2y92vvt.cloudfront.net/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
```

nos descarga un zip, lo descomprimimos

```
unzip awscliv2.zip
```

instalamos el ejecutable descomprimido

```
sudo ./aws/install
```

probamos

```
aws2
aws2 --version
```

como ya esta instalado, eliminamos el archivo zip y la carpeta descomrimida

```
rm -rf aws*
```

ahora nos ubicamos en la carpeta de binarios

```
cd /usr/local/bin
```

vamos a crear un enlace directo a aws2

```
sudo ln -s aws2 aws
```

se creo el enlace y probamos

```
aws
aws --version
```

y asi tenemos configurado nuestro entorno :)

## Creamos nuestra primera imagen de docker


creamos nuestro dockerfile

```
from Ubuntu:12.04

run apt-get update
run apt-get install nginx -y
run echo "hello world" > /var/www/html/index.html

cmd nginx -g 'daemon off;'

```
creamos nuesra imagen

```
docker build .
```

verificamos que se haya creado

```
docker images
```

eliminamos nuestra imagen

```
docker image rm (codigounicodeimagen)
```

volvemos a crear nuestra imagen y le agregamos un nombre y tag

```
docker build -t helloworld:1.0 .
```

agregamos un nuevo tag a nuestra imagen

```
docker tag helloworld:1.0 helloworld:latest
```

probamos

```
docker images
```

ahora vamos a ver los metadatos de nuestra imagen

```
docker inspect helloworld:latest
```

## Ciclo de vida de un contenedor

creamos un contenedor en base a la imagen creada anteriormente

docker create helloworld:latest

al crear el contenedor, se crea con un nombre aleatorio

```
docker ps
```

no vemos todavia nuestro contenedor

```
docker ps -a
```

vemos nuestro contenedor que esta parado

```
docker start (codigo unico)
docker ps
```

vemos el contenedor corriendp

paramos el contenedor

```
docjer stop (codigo unico)
docke ps
```

no hay contenedor

volvemos a cargarlo

```
docker start (nombre aleatorio del contenedor)
```

primero paramos el contenedor para luego eliminarlo

```
docker stop (nombre aleatorio del contenedor)
docker rm (nombre aleatorio del contenedor)
```

si deseamos eliminar su imagen

```
docker image rm (codigo unico)
```

ahora creamos un contenedor y le vamos a asignar un nombre

```
docker create --name web01 helloworld:latest
```

verificamos

```
docker ps -a
```

hacemos prueba con nuestro nuevo contenedor

```
docker start web01
docker stop web01
docker rm web01
```

## Exponer Puertos

inspeccionamos nuestro contenedor

```
docker inspect web01
```

vemos en nuestra metadata la configuracion del contenedor, por ahora nos fijamos en la direccion ip de nuestro contenedor

accedemos a los puertos de nuestro contenedor a través de esta ip que esta en la configuracion de la imagen

hacemos una prueba en el puerto 80

```
curl 172.17.0.2
```

por ahora el contenedor no es accesible desde fuera del host

eliminamos el contenedor para hacer unas prueba

```
docker rm web01 -f
```

volvemos a crear nuestro contenedor, pero ahora con una configuracion diferente

```
docker create -p 8080:80 --name web01 helloworld:latest
```

con esto ahora accedemos al puerto 80 expuesto de nuestro contenedor desde el puerto 8080 de nuestro host

```
docker start web01
docker inspect web01
```

vemos que la ip se mantiene

```
curl 127.17.0.2
curl localhost:8080
```

accedemos normalmente desde nuestro host

hacemos una nueva prueba

```
docker rm web01 -f
docker create -p 127.0.0.1:8080:80 --name web01 helloworld:latest
```

ahora accedemos solo desde la ip 127.0.0.1 al puerto 80 de nuestro contenedor

```
docker start web01
```

verificamos

```
curl localhost:8080
```

ahora hagamos una nueva prueba

```
docker rm web -f
```

modificamos nuestro dockerfile agregamos la siguiente linea de codigo

```
EXPOSE 80
```

volvemos a crear la imagen

```
docker build -t helloworld:1.0
docker tag helloworld:latest helloworld:1.0
```

veamos el resultado de nuestra nueva configuracion

```
docker image inspect helloworld:latest
```

vemos los puesrtos expuestos "EXPOSEDPORTS"

ahora vamos a publicar sobre los puertos expuestos de nuestra imagen "PUBLISH ALL"

```
docker create -P --name web01 helloworld:latest
docker start
docker ps
```

vemos que en la columna puertos se va a mapear el puerto 80 a un puerto random

```
docker stop web01
docker start web01
docker ps
```

ahora vemos que se ha mapeado nuestro contenedor con otro puerto del host

```
curl localhost:32769
```

este rango de puertos es configurable desde 

```
cat /proc/sys/net/ipv4/ip_local_port_range
```

vemos que tiene un rango desde 32768 al 68999 (este depende de la version del linux), el proceso que se encarga de estos mapeos se llama docker proxy

```
ps aux|grep docker-proxy
```

este es el proceso que se va a encargar las reglas de ip tables

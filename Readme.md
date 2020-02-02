# Curso kubernetes y docker en aws desde cero

link del [curso](https://www.udemy.com/course/kubernetes-y-docker-en-aws-desde-cero/)

## Primer Paso

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

```docker ps```

Para tener acceso a nuestro usuario a docker, lanzamos el siguiente comando
Añadir un nuevo grupo al usuario

```usermod -a -G docker rodrigo```

reiniciamos nuestra maquina y probamos que podemos consultar docker desde la raiz

Ahora Instalamos [kubectl](https://kubernetes.io/es/docs/tasks/tools/install-kubectl/)

usarmos la instalacion de binario curl

```
curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl
```

nos descarga el binario en la raiz

probamos ejecutarlo

```./kubectl```

pero nos da denegacion de permiso, debemos acceder al permiso

```chmod +x kubectl```

ahora probamos y vemos la configuracion, tambien podemos ver la version

```
./kubectl
./kubectl version
```

movemos el binario la carpeta de binarios

```mv kubectl /usr/local/bin/```

Ahora instalamos [minikube](https://kubernetes.io/es/docs/tasks/tools/install-minikube/)

ejecutamos el siguiente comando

```
curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64 \
  && chmod +x minikube
```

movemos el binario a la carpeta de binarios

```sudo mv minikube /usr/local/bin```

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

```sudo chmod +x docker-compose```

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

```unzip awscliv2.zip```

instalamos el ejecutable descomprimido

```sudo ./aws/install```

probamos

```
aws2
aws2 --version
```

como ya esta instalado, eliminamos el archivo zip y la carpeta descomrimida

```rm -rf aws*```

ahora nos ubicamos en la carpeta de binarios

```cd /usr/local/bin```

vamos a crear un enlace directo a aws2

```sudo ln -s aws2 aws```

se creo el enlace y probamos

```
aws
aws --version
```

y asi tenemos configurado nuestro entorno :)
# -Starting-with-Jenkins
starting with the continuous integration of jenkins
# JENKINS 

## Que ofrece?

Ofrece la integración continua 
La integración continua es una práctica de desarrollo software donde los miembros del equipo integran su trabajo frecuentemente.

Cada integración se verifica compilando el código fuente y obteniendo un ejecutable (a esto se le llama build, y debe hacerse de forma automatizada).Además también se pasan las pruebas y métricas de calidad para detectar los errores tan pronto como sea posible.

Al integrar frecuentemente el código, y con la ayuda de herramientas como Jenkins, puedes saber el estado del software en todo momento. Sabes qué funciona, qué no y qué errores hay.

También puedes monitorizar la calidad del código y su cobertura de pruebas.

## Qué es?

Jenkins es un servidor de integración continua, gratuito, open-source y actualmente uno de los más empleados para esta función. 

La base de Jenkins son las tareas, donde indicamos qué es lo que hay que hacer en un build. Por ejemplo, podríamos programar una tarea en la que se compruebe el repositorio de control de versiones cada cierto tiempo, y cuando un desarrollador quiera subir su código al control de versiones, este se compile y se ejecuten las pruebas.

Si el resultado no es el esperado o hay algún error, Jenkins notificará al desarrollador, al equipo de QA, por email o cualquier otro medio, para que lo solucione. Si el build es correcto, podremos indicar a Jenkins que intente integrar el código y subirlo al repositorio de control de versiones.

Actúa como herramienta que sirve de enlace en todo el proceso de desarrollo.

Desde Jenkins podrás indicar que se lancen métricas de calidad y visualizar los resultados dentro de la misma herramienta. También podrás ver el resultado de los tests, generar y visualizar la documentación del proyecto o incluso pasar una versión estable del software al entorno de QA para ser probado, a pre-producción o producción.


## Uso:

Existen 3 formas de desplegar aṕlicaciones en jenkins
Despliegue de forma local
            Manejo de archivos de la VM directamente  (manejo del sistema de archivos etc, var)

![GitHub Logo](/images/logo.png)

Despliegue de forma remota
Comunicarse con las máquinas virtuales a través de ssh

![GitHub Logo](/images/logo.png)

Despliegue por JNLP
levantar
conectar
controlar de forma local
compila
lo guarda en zip
docker img (dockerfile)

![GitHub Logo](/images/logo.png)

## Pasos a seguir:
Instalar jenkins
instalar plugins
Conectar jenkins con el API de docker
Construir JNLP slaves
Pruebas
Realizar proyectos
Pipelines

### Paso 1:

       a) Entrar a jenkins
       b) Crear un directorio en var/conteiners/jenkins
       c) Correr el comando :
          docker run -p 8080:8080 -p 5050:5050 -v /var/conteiners/jenkins -td
       d) Dar permisos al directorio
          chown 1000:1000 -R /var/conteiners/jenkins
       e) Ver estado del contenedor
          docker logs -f

### Paso 2:
      a) Ingresar al dominio
         https://jenkins.ctin.amxdigital.net
      b) Configurar el proxy
          Servidor -> 10.0.202.7
          Puerto -> 80:80
      c) Seleccionar plugins
         (seleccionar todos los plugins que se desean instalar)

     d) Crear primer usuario 

### Paso 3: Conectar con el Api docker

     a) Dentro de jablab
     b) Dentro de build
     c) abrir el archivo:
        vi  /etc/sysconfig/doker
        dentro hay que vandear todas las interfaces de docker 
        OPTIONS='--selinux-enabled -H unix:///var/run/docker.sock -H tcp://0.0.0.0:2376'
     d) Reiniciar el servidor
         systemctl restart docker
     e) Revisar
         netstat -pantu

NOTA: En caso de que aparezca “No route to host” significa que esta prendida el firewally me esta bloqueando:

systemctl stop firewall
systemctl restart docker 


### Paso 4: Construir JNLP slaves

      a) Dentro de builder
      b) Crear un directorio 
         mkdir -p /opt/Docker-files 
      c) Dentro de directorio, habra que descomprimir el archivo -->  slaves.tgr.gz
      d) Entrar a las carpetas:

cd centos73_base

cd O_openjdk8

y dentro contruir el dokerfile
docker build -t openjdk:8-jdk --build-arg http_proxy=http://10.0.202.7:8080 --build-arg https_proxy=https://10.0.202.7:8080 .

cd 1-openjdk8
docker build -t jenkins/slave:3.16-1 --build-arg http_proxy=http://10.0.202.7:8080 --build-arg https_proxy=https://10.0.202.7:8080 .


cd 2-openjdk8.
docker build -t ctin-slaves --build-arg http_proxy=http://10.0.202.7:8080 --build-arg https_proxy=https://10.0.202.7:8080 .


###Paso 5: Configurar la nube (Decirle a jenkins como comunicarse con esclavos)

a) Dentro de jenkins
administrar jenkins/ configurar el sistem/ añadir una nueva nube

Name: dockernube
Docker HostURL: tcp://10.0.57.32:2376
Conteiner cap:5
Docker images:ctin slaves
Connect method: conect with JNLP
Jenkins URL: https://jenkins.ctin.amxdigital.net/
Establecer un tunel a travez de:
10.0.8.152:50000
Label: workers
Extrahosts: https://jenkins.ctin.amxdigital.net:10.0.8.204

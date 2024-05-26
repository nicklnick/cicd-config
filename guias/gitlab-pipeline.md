# Creación de un pipeline en GitLab

A grandes rasgos, el proceso de despliegue denota los siguientes pasos:

1. Se realiza una operación de `git push` al repositorio, promoviendo una nueva versión del software
2. El pipeline del `runner` es ejecutado, realizando pruebas unitarias (u otros procesos) y a la empaquetación de una imagen de `Docker` correspondiente a la nueva versión del software como un servicio listo para desplegar
3. Esta imagen es subida a un registro privado de [GitLab Container Registry](https://docs.gitlab.com/ee/user/packages/container_registry/)
4. Se le envía a la instancia de producción la imagen generada y esta se levanta como servicio, concretando el despliegue y exponiendo la nueva versión del software al público.

Comenzaremos dando de alta un registro de contenedores dentro de nuestro repositorio con GitLab Container Registry.

## 1. Creación de la instancia de producción

Deberemos crear otra instancia EC2 además de la del Runner, para que actúe como nuestro servidor de producción. Si seguiste [esta guía](./prerrequisitos/ec2-runner.md), podes volver a verla pero cambiando las siguientes variables:

- Cambiar el nombre a `prod` en lugar de `runner`
- Así también generar otro par de claves SSH, la cual denominaremos `prod_key` generando así el archivo `prod_key.pem`
- También configuraremos un grupo de seguridad adecuado para poder acceder al contenido de forma pública

Luego de crear la instancia, siguiendo con lo explicado en secciones anteriores, nos conectamos mediante SSH e instalamos por el momento solo _Docker_ (si no recordás cómo hacerlo mirá [esta guía](./prerrequisitos/ec2-docker.md)).

## 2. Configuración de acceso a EC2 de producción

Tal como se nombró previamente, debemos tener una forma de comunicación entre la instancia del `runner` y la instancia de producción `prod`. Hemos generado otra clave privada denominada`prod_key`, la cual vamos a utilizar ahora para generar el acceso a la instancia de `prod` desde el `runner`.

Sin embargo, en lugar de utilizar el archivo `.pem` para acceder a la instancia de producción, haremos uso de las variables de entorno CI/CD que nos ofrece GitLab. Vamos a seguir una de las practicas que exponen en su documentación sobre [cómo utilizar claves SSH dentro de un pipeline de CI/CD](<https://docs.gitlab.com/ee/ci/ssh_keys/#:~:text=To%20create%20and%20use%20an,a%20newline%20(%20LF%20character).>).

1. En el menú de la izquierda de la pantalla principal de nuetro repositorio, clickeamos "Settings" > "CI/CD".
2. A diferencia de los pasos anteriores elegiremos como "Type" una variable de tipo `File`. Elegimos una "Key" para la variable. Esta "Key" será la forma en la cual nos referiremos a la misma dentro de la definición del Pipeline, en nuestro caso `PROD_SSH_KEY`

   <img src="../img/guias/gitlab-pipeline-prod_access-paso2.png" width="50%"/>

3. A continuación debemos colocar el valor que contendrá esta clave. Para esto copiaremos el contenido del archivo `prod_key.pem` que descargamos previamente desde AWS y lo pegaremos como valor de esta variable `PROD_SSH_KEY`.
   <img src="../img/guias/gitlab-pipeline-prod_access-paso3.png" width="50%"/>

> [!IMPORTANT]
> Tal como indica la documentación de GitLab, es importante dejar una nueva línea al final del contenido colocado en el campo de "Value".

4. Clickeamos en "Add variable" y la variable será creada. El valor de la variable sólo puede ser visto por los dueños del repositorio (aunque esto puede configurarse).

## 3. Dockerizando la App de Next.js

Ya teniendo los accesos a las instancias preparados, podemos pasar a la creación del pipeline de despliegue. Pero primero también hará falta definir un `Dockerfile` que describa cómo se debe empaquetar la aplicación de Next.js.

Dentro de la carpeta raíz del proyecto, crearemos un archivo Dockerfile , con el siguiente contenido:

```docker
# Usar la imagen oficial de Node.js como imagen base
FROM node:22-alpine3.18

# Establecer el directorio de trabajo en el contenedor
WORKDIR /usr/src/app

# Copiar package.json y package-lock.json al directorio de trabajo
COPY package*.json ./

# Instalar dependencias
RUN npm install

# Copiar el resto del código de la aplicación al directorio de trabajo
COPY . .

EXPOSE 3000

# Ejecutar el comando para iniciar la aplicación
CMD ["npm", "run", "dev"]
```

Una vez listo el archivo `Dockerfile`, lo subimos al repositorio y el mismo será consumido durante el pipeline en la etapa correspondiente.

## 4. Creando un pipeline de despliegue
HACER

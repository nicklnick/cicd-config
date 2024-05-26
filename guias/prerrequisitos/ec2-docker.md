## Instalación de Docker en la instancia EC2

Para que nuestra instancia EC2 pueda funcionar como GitLab Runner, debemos instalar Docker.

## Instalando Docker

1. Utilizaremos `apt-get` para instalar Docker . Por esto, es importante en primer lugar actualizar el índice de paquetes para obtener las últimas versiones de los mismos:

   ```bash
   sudo apt-get update
   ```

2. Ahora realizamos la instalación de Docker con el siguiente comando:

   ```bash
   sudo apt-get install docker.io -y
   ```

3. Luego de instalar Docker, debemos inicializar el _Docker Service_:

   ```bash
   sudo systemctl start docker
   ```

4. Podemos verificar que la instalación de Docker haya sido exitosa, mediante el comando:

   ```bash
   sudo docker run hello-world
   ```

5. Si todo ha salido bien, podemos establecer que el servicio de Docker comience automáticamente cada vez que la instancia se active. Para esto ejecutamos:

   ```bash
   sudo systemctl enable docker
   ```

6. Finalmente, es recomendable añadir al usuario actual del sistema operativo como usuario Docker para poder correr comandos `docker` sin la necesidad del prefijo `sudo`:

   ```bash
   sudo usermod -a -G docker $(whoami)
   ```

De esta manera Docker quedará listo para ser utilizado por el GitLab Runner en nuestra EC2.

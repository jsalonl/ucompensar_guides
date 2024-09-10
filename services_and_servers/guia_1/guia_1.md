# Repaso redes

## Objetivo

El objetivo de esta práctica es que los estudiantes instalen y configuren un entorno de desarrollo completo utilizando el stack **LAPP** (Linux, Apache, PostgreSQL y PHP).

A través de esta actividad, los estudiantes aprenderán a instalar y configurar cada componente del stack, comprendiendo la interacción entre ellos y cómo se integran para crear un entorno de servidor web funcional con soporte para bases de datos PostgreSQL y ejecución de scripts PHP. Al finalizar, los estudiantes tendrán un servidor LAPP operativo que podrán utilizar como base para futuros proyectos y desarrollos web.

## Requisitos

- Computador:
  - **Procesador**: Core i3 o superior
  - **RAM**: 8 GB o superior
  - **Disco**: 25 GB o superior

- Software:
  - **Sistema operativo**: Windows (Si bien puede emularse en MacOS y Linux se recomienda windows)
  - **[Packet Tracer](https://www.virtualbox.org/wiki/Downloads)**: Nos permite emular dispositivos cisco.

- Conexión a internet

## Procedimiento

1. Conectarnos a la máquina virtual, haciendo uso de putty y sabiendo la ip de la máquina como vemos en la siguiente captura, damos click en `Open` o enter

   ![Imagen 1](./image1.png)

2. Ingresamos nuestro usuario y contraseña, si todo esta bien nos mostrara el banner de acceso similar a esto:

    ![Imagen 2](./image2.png)

3. Vamos a actualizar tanto el sistema como los paquetes, con el siguiente comando.

    *Nota: Es posible que nos pida clave por ser la primera instrucción con sudo (usuario administrativo) de ahí en adelante no debería pedir confirmación de la clave*

    ```sh
    sudo apt update && sudo apt upgrade -y
    ```

4. Si nos llega a salir algo similar a esto, solo damos enter sobre `Ok`

    ![Imagen 3](./image3.png)

5. Instalar dependencias.

   Ahora debemos instalar las dependencias que vamos a necesitar, para ello ejecutamos en la consola lo siguiente:

   ```sh
   sudo apt install apache2 postgresql postgresql-contrib php libapache2-mod-php php-pgsql git -y
   ```

   > Entendiendo que estamos haciendo: Estamos instalando varios paquetes:
   >
   > **Apache**: es un servidor web que permite servir contenido web a través del protocolo HTTP. Es uno de los servidores web más utilizados en el mundo debido a su robustez, flexibilidad y soporte para una amplia variedad de lenguajes y módulos.
   >
   > **PostgreSQL**: es un sistema de gestión de bases de datos relacional (RDBMS) de código abierto, conocido por su robustez, escalabilidad y soporte avanzado para consultas SQL complejas. Es una opción popular para almacenar y gestionar datos de manera eficiente.
   > Estamos instalando tanto el servidor como librerias adicionales
   >
   > **PHP**: es un lenguaje de scripting del lado del servidor que se utiliza para desarrollar aplicaciones web dinámicas. Es compatible con una amplia variedad de bases de datos, incluido PostgreSQL, y es esencial para ejecutar aplicaciones web desarrolladas en este lenguaje.
   >
   > **Git**: es un sistema de control de versiones distribuido que facilita el seguimiento de cambios en el código fuente de un proyecto. Es ampliamente utilizado en el desarrollo de software para gestionar el código, colaborar con otros desarrolladores, y mantener un historial de versiones.

   Empezará la instalación de módulos y paquetes (depende la conexión a internet este proceso puede tardar)

   ![Imagen 4](./image4.png)

   *Nota: Si nos aparece alguna ventana para seleccionar entre un usuario por defecto y un usuario 1000, simplemente damos en OK sin seleccionar ninguno*

6. Configuración Apache

   Vamos a habilitar el servicio de apache al inicio (es decir, cada vez que la máquina inicie, levantará el servicio)

   ```shell
   sudo systemctl enable apache2
   ```

   Si todo sale bien nos debe mostrar algo asi:

   ![Imagen 5](./image5.png)

   Para probar que funciona, podemos validar el estado del servicio, con el comando:

   ```shell
   sudo systemctl status apache2
   ```

   Que mostrará algo asi:

   ![Imagen 6](./image6.png)

   Para salir, simplemente con la combinación de teclas ctrl + C

   Ahora en una ventana de nuestro sistema operativo anfitrión (Windows) vamos a abrir la IP de nuestra máquina virtual (en nuestro caso la 192.168.20.201), si todo funciona bien, veremos algo asi:

   ![Imagen 7](./image7.png)

7. Validar instalación de PHP

   Para confirmar que nuestro PHP esta correctamente instalado vamos a crear un archivo nuevo en la carpeta raíz de apache, a través del siguiente comando:

   ```shell
   echo "<?php phpinfo(); ?>" | sudo tee /var/www/html/info.php
   ```

   > Entendiendo que estamos haciendo:
   >
   > `echo "<?php phpinfo(); ?>"`: Este comando echo genera una línea de código PHP que ejecuta la función phpinfo(). Esta función muestra información detallada sobre la configuración de PHP en el servidor, incluyendo variables de entorno, módulos instalados, configuraciones de PHP, y mucho más.
   >
   > `|`: El símbolo de pipe ( | ) se utiliza para redirigir la salida del comando echo al siguiente comando en la línea, en lugar de mostrarlo en la pantalla.
   >
   > `sudo tee /var/www/html/info.php`: El comando tee recibe la salida del echo y la escribe en un archivo llamado info.php dentro del directorio /var/www/html/. sudo se usa para asegurarse de que tienes los permisos necesarios para escribir en ese directorio, que es el directorio raíz de Apache donde se alojan los archivos web por defecto.
   >
   > `/var/www/html/info.php`: Esta es la ruta completa donde se creará el archivo info.php. Al colocar este archivo en el directorio raíz de Apache, será accesible desde un navegador web.

   Para comprobar que esta todo bien, vamos nuevamente a nuestro navegador web del sistema anfitrión (Windows) y en la ip agregamos `/info.php`, donde debe mostrarnos algo asi:

   ![Imagen 8](./image8.png)

8. Probando git

   Vamos a confirmar la correcta instalación de git, para ello ejecutamos

   ```shell
   git --version
   ```

   Nos debe mostrar algo asi:

   ![Imagen 9](./image9.png)

9. Configurando PostgreSQL

   Vamos a habilitar el servicio de base de datos PostgreSQL al inicio (es decir, cada vez que la máquina inicie, levantará el servicio)

   ```shell
   sudo systemctl enable postgresql
   ```

   Si todo sale bien nos debe mostrar algo asi:

   ![Imagen 10](./image10.png)

   Para probar que funciona, podemos validar el estado del servicio, con el comando:

   ```shell
   sudo systemctl status postgresql
   ```

   Que mostrará algo asi:

   ![Imagen 11](./image11.png)

   Para salir, simplemente con la combinación de teclas ctrl + C

   Si queremos verificar la consola de PostgreSQL debemos loguearnos con el usuario postgres (quien tiene permisos de administrador hacia el serviciod de base de datos)

   ```shell
   sudo -i -u postgres
   ```

   Aca cambiará nuestro usuario a postgres y ejecutaremos el comando:

   ```shell
   psql
   ```

   Veremos la consola psql, donde podremos ver las bases de datos con el comando:

   ```shell
   \l
   ```

   El resultado será este:

   ![Imagen 12](image12.png)

   Para salir usamos el comando `\q` y luego el comando `exit`

   ![Imagen 13](image13.png)

## Conclusiones

1. **Instalación Exitosa del Stack LAPP**: A través de esta práctica, hemos logrado instalar y configurar con éxito un entorno LAPP (Linux, Apache, PostgreSQL y PHP) en un servidor Ubuntu. Este entorno es fundamental para el desarrollo de aplicaciones web robustas y escalables.

2. **Configuración de Servicios**: Hemos aprendido a habilitar y verificar la correcta operación de servicios críticos como Apache y PostgreSQL. Esto garantiza que el servidor web y el sistema de gestión de bases de datos estén disponibles y operativos en todo momento.

3. **Integración de Componentes**: La práctica ha demostrado cómo los diferentes componentes del stack LAPP se integran para proporcionar una plataforma completa para el desarrollo web. Apache sirve como el servidor web, PHP maneja la lógica del lado del servidor, y PostgreSQL gestiona la persistencia de datos.

4. **Uso de Git**: También hemos configurado Git, una herramienta esencial para el control de versiones en el desarrollo de software. Esta configuración permitirá a los estudiantes mantener un historial detallado de los cambios en su código y colaborar eficazmente en proyectos futuros.

5. **Manejo Básico de PostgreSQL**: Los estudiantes han tenido la oportunidad de familiarizarse con PostgreSQL, desde la instalación hasta la ejecución de comandos básicos dentro de su consola. Esto les brinda una base sólida para trabajar con bases de datos en proyectos más complejos.

6. **Comprensión de la Configuración y Seguridad del Sistema**: Además de instalar los componentes del stack, hemos configurado el sistema para que inicie los servicios de Apache y PostgreSQL automáticamente al encender la máquina. Esto no solo es conveniente, sino que también mejora la seguridad y la fiabilidad del servidor.
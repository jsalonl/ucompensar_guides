# Repaso redes de telecomunicaciones

## Objetivo

El objetivo de esta práctica es que los estudiantes puedan tener un repaso de lo visto en semestres anteriores referente a routing y switching en un entorno laboral real, para dar paso a la configuración de seguriad en equipos Cisco e iniciar el módulo con otros dispositivos de red.

## Requisitos

- Computador:
  - **Procesador**: Core i3 o superior
  - **RAM**: 8 GB o superior
  - **Disco**: 25 GB o superior

- Software:
  - **Sistema operativo**: Windows (Si bien puede emularse en MacOS y Linux se recomienda windows)
  - **[Packet Tracer](https://www.netacad.com/resources/lab-downloads?courseLang=es-XL)**: Nos permite emular dispositivos cisco (Se debe registrar o ingresar a la plataforma netacad).

- Conexión a internet

## Contexto

1. Suponga una empresa que tiene 3 sedes comunicadas entre sí a través de una red LAN

2. Vamos a generar una red similar a esta:

   ![Imagen 1](image.png)

   Físicamente la red se verá asi:

   ![Imagen 2](image1.png)

3. Identificamos os dispositivos usados, los cuales son:

   - 3 switch modelo 2960-24TT
   - 1 switch modelo PT Empty (con 4 módulos **NM-1CGE** instalados)
   - 1 router modelo 4321
   - 3 PC modelo PT
   - 3 laptop modelo PT
   - 1 servidor modelo PT

4. La configuración de red será la siguiente:

    |Nombre|VLAN|Red|
    |--|--|--|
    |Gerencia|10|10.0.0.0/26|
    |Operativos|20|10.0.0.64/26|
    |Infraestructura|30|10.0.0.128/26|
    |Gestión|40|10.0.0.192/26|

## Procedimiento

### Descargue el laboratorio

[Lab Repaso Redes](https://drive.google.com/file/d/1CMrxj3xzxRRqce1YaIteO70CAkLqeh6V/view?usp=sharing)

### Configuración Router principal sede 1

1. Acceder a modo privilegiado:

   ```bash
   enable
   ```

2. Acceder a modo de configuración:

   ```bash
   conf t
   ```

3. Configurar nombre, en este caso pondremos R1:

   ```bash
   hostname R1
   ```

4. Desactivar la búsqueda de nombres de dominio cuando se introduce un comando incorrecto o una dirección no válida en el dispositivo:

   ```bash
   no ip domain-lookup
   ```

5. Creamos las VLAN y sus segmentos de red asociados:
   1. VLAN de gerencia

      ```bash
      interface gigabitEthernet 0/0/0.10
      encapsulation dot1Q 10
      ip address 10.0.0.1 255.255.255.192
      exit
      ```

   2. VLAN de operativos

      ```bash
      interface gigabitEthernet 0/0/0.20
      encapsulation dot1Q 20
      ip address 10.0.0.65 255.255.255.192
      exit
      ```

   3. VLAN de infraestructura

      ```bash
      interface gigabitEthernet 0/0/0.30
      encapsulation dot1Q 30
      ip address 10.0.0.129 255.255.255.192
      exit
      ```

   4. VLAN de gestión

      ```bash
      interface gigabitEthernet 0/0/0.40
      encapsulation dot1Q 40
      ip address 10.0.0.193 255.255.255.192
      exit
      ```
  
    > Entendiendo que estamos haciendo:
    >
    > `interface gigabitEthernet 0/0/0.10`: Esto crea una subinterfaz llamada GigabitEthernet 0/0/0.10. Cada subinterfaz representa una VLAN específica en un enlace troncal (trunk). El número .10 al final de la interfaz indica que esta subinterfaz estará asociada a la VLAN 10.
    >
    > `encapsulation dot1Q 10`: Este comando activa la encapsulación 802.1Q, que es el protocolo utilizado para transportar tráfico de múltiples VLANs en un enlace troncal. El número 10 indica que esta subinterfaz se asociará con la VLAN 10. Cualquier tráfico que llegue al router etiquetado con el ID de VLAN 10 será procesado por esta subinterfaz.
    >
    > `ip address 10.0.0.1 255.255.255.192`: Aquí asignamos una dirección IP a la subinterfaz. Esta dirección 10.0.0.1 será la puerta de enlace para los dispositivos en la VLAN 10 (Gerencia). La máscara de subred 255.255.255.192 define que la red es de tipo /26 (64 hosts por subred).
    >
    > `exit`: Este comando cierra la configuración de la subinterfaz y vuelve al modo de configuración global.

6. Activamos la interfaz:

   ```bash
   int g0/0/0
   no shutdown
   ```

7. Guardar los cambios:
   De momento no hemos guardado los cambios y si llegaramos a apagar el equipo perderiamos la configuración, por ello se debe escribir en el modo privilegiado el siguiente comando:

   ```bash
   end
   wr
   ```

    > Entendiendo que estamos haciendo:
    >
    > `end`: Este comando te saca del modo de configuración y vuelve al modo privilegiado (enable mode). Es una forma de salir de cualquier modo de configuración de una interfaz o línea, devolviéndote al nivel superior del CLI donde puedes ejecutar comandos globales.
    >
    > `wr`: Es una abreviatura para el comando write memory, que guarda la configuración actual (configuración en ejecución) en la memoria de inicio (startup-config). Esto asegura que los cambios se mantendrán después de un reinicio del router.

## Configuración Switch principal sede 1

1. Lo primero es notar que este switch tiene 4 interfaces gigabit diferentes, los cuales son:

   - Gig6/1
   - Gig7/1
   - Gig8/1
   - Gig9/1

2. Nombramos al equipo

   ````shell
   enable
   conf t
   hostname SW1R1
   ````

3. Nuestro switch debe conocer las vlan para ello vamos a configurarlas:

   1. VLAN de gerencia

      ```shell
      vlan 10
      name gerencia
      exit
      ```

   2. VLAN de operativos

      ```shell
      vlan 20
      name operativos
      exit
      ```

   3. VLAN de infraestructura

      ```shell
      vlan 30
      name infraestructura
      exit
      ```

   4. VLAN de gestión

      ```shell
      vlan 40
      name gestion
      exit
      ```

4. Para cada uno de las interfaces vamos a asignarlas en modo troncal:

   1. Gigabit 6/1

      ```shell
      interface gi6/1
      switchport mode trunk
      switchport trunk allowed vlan 10,20,30,40
      no shutdown
      exit
      ```

   2. Gigabit 7/1

      ```shell
      interface gi7/1
      switchport mode trunk
      switchport trunk allowed vlan 10,20,30,40
      no shutdown
      exit
      ```

   3. Gigabit 8/1

      ```shell
      interface gi8/1
      switchport mode trunk
      switchport trunk allowed vlan 10,20,30,40
      no shutdown
      exit
      ```

   4. Gigabit 9/1

      ```shell
      interface gi9/1
      switchport mode trunk
      switchport trunk allowed vlan 10,20,30,40
      no shutdown
      exit
      ```

5. Agregamos la ip de gestión

   ```shell
   interface vlan 40
   ip address 10.0.0.194 255.255.255.128
   no shutdown
   ```

6. Guardamos cambios

   ```shell
   end
   wr
   ```

## Configuración Switch distribución sede 1

1. Nombramos al equipo

   ````shell
   enable
   conf t
   hostname SW2R1
   ````

2. Nuestro switch debe conocer las vlan para ello vamos a configurarlas:

   1. VLAN de gerencia

      ```shell
      vlan 10
      name gerencia
      exit
      ```

   2. VLAN de operativos

      ```shell
      vlan 20
      name operativos
      exit
      ```

   3. VLAN de infraestructura

      ```shell
      vlan 30
      name infraestructura
      exit
      ```

   4. VLAN de gestión

      ```shell
      vlan 40
      name gestion
      exit
      ```

3. Nuestros puertos Gigabit Ethernet son troncales.

   ```shell
   interface range gi0/1-2
   switchport mode trunk
   switchport trunk allowed vlan 10,20,30,40
   no shutdown
   exit
   ```

4. Configuramos nuestros puertos de acceso, definidos asi:

   - Puertos del 1 al 8: gerencia

     ```shell
     interface range fa0/1-8
     switchport mode access
     switchport access vlan 10
     no shutdown
     exit
     ```

   - Puertos del 9 al 16: operativos

     ```shell
     interface range fa0/9-16
     switchport mode access
     switchport access vlan 20
     no shutdown
     exit
     ```

   - Puertos del 17 al 24: infraestructura

     ```shell
     interface range fa0/17-24
     switchport mode access
     switchport access vlan 30
     no shutdown
     exit
     ```

5. Agregamos la ip de gestión

   ```shell
   interface vlan 40
   ip address 10.0.0.195 255.255.255.128
   no shutdown
   ```

6. Guardamos cambios

   ```shell
   end
   wr
   ```

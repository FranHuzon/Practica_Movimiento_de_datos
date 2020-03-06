### 1. Importad el fichero resultante de la exportación completa de las tablas y los datos de una instancia de ORACLE en otra instancia diferente empleando el comando impdp y explicad qué problemas surgen. Realizad un remapeo de esquemas si es necesario.

Al realizar una primera importación vimos que daba una cantidad de fallos enorme, y averiguamos que no había que exportar el esquema de los usuarios del sistema. Para ver los usuarios del sistema usamos la siguiente consulta:

~~~
SELECT username FROM dba_users WHERE to_char(created, 'YYYY') = (SELECT to_char(created, 'YYYY') FROM dba_users WHERE username = 'SYS');
~~~

![grupal0](https://github.com/FranHuzon/Practica_Movimiento_de_datos/blob/master/images/grupal0.png)

Según la documentación oficial, hay que crear un directorio desde la terminal de oracle y darle permisos de lectura y escritura al usuario que vaya a realizar la exportación:

~~~
SQL> CREATE OR REPLACE DIRECTORY test_dir AS '/opt/oracle/oradata/';

Directorio creado.

SQL> GRANT READ, WRITE ON DIRECTORY test_dir TO SYSTEM;

Concesión terminada correctamente.

SQL>
~~~

Luego, hemos exportado los datos excluyendo el esquema de los usuarios anteriores con el comando:

~~~
expdp SYSTEM/RAUL@orcl full=Y directory=TEST_DIR dumpfile=expdp02.dmp logfile=expdpDB10G.log EXCLUDE=SCHEMA:\"IN \(\'ORACLE_OCM\',\'OJVMSYS\',\'SYSKM\',\'XS$NULL\',\'GSMCATUSER\',\'MDDATA\',\'SYSBACKUP\',\'DIP\',\'SYSDG\',\'APEX_PUBLIC_USER\',\'SPATIAL_CSW_ADMIN_USR\',\'SPATIAL_WFS_ADMIN_USR\',\'GSMUSER\',\'AUDSYS\',\'FLOWS_FILES\',\'DVF\',\'MDSYS\',\'ORDSYS\',\'DBSNMP\',\'WMSYS\',\'APEX_040200\',\'APPQOSSYS\',\'GSMADMIN_INTERNAL\',\'ORDDATA\',\'CTXSYS\',\'ANONYMOUS\',\'XDB\',\'ORDPLUGINS\',\'DVSYS\',\'SI_INFORMTN_SCHEMA\',\'OLAPSYS\',\'LBACSYS\',\'OUTLN\',\'SYSTEM\',\'SYS\'\)\"
~~~

**Importante: Prestar atención a que todo los caracteres especiales estén escapados correctamente**

La ip de la máquina exportada es:

![ip1](https://github.com/FranHuzon/Practica_Movimiento_de_datos/blob/master/images/ip1.png)

![grupal01](https://github.com/FranHuzon/Practica_Movimiento_de_datos/blob/master/images/grupal01.png)
![grupal02](https://github.com/FranHuzon/Practica_Movimiento_de_datos/blob/master/images/grupal02.png)

Realizamos nuestra primera importación en una máquina que no estaba limpia, es decir, ya tenía, tablas creadas y datos dentro. Éste fue el resultado:

La ip de la máquina importada es:

![ip3](https://github.com/FranHuzon/Practica_Movimiento_de_datos/blob/master/images/ip3.png)

![grupal1](https://github.com/FranHuzon/Practica_Movimiento_de_datos/blob/master/images/grupal1.png)
![grupal2](https://github.com/FranHuzon/Practica_Movimiento_de_datos/blob/master/images/grupal2.png)

Nos dimos cuenta que algunos pasos no se realizaban correctamente porque no existía el usuario en la nueva base de datos. Entonces realizamos una segunda exportación en una máquina nueva creada mediante una OVA de Virtualbox de la máquina anterior, previamente introduciendo a mano los usuarios y los roles que no existían en la nueva base de datos:

~~~
CREATE USER PRUEBA1 IDENTIFIED BY "PRUEBA1";
CREATE USER USER_PRIVS IDENTIFIED BY "USER_PRIVS";
CREATE USER JESUS IDENTIFIED BY "JESUS";
CREATE USER SCOTT IDENTIFIED BY "SCOTT";
CREATE USER FRAN IDENTIFIED BY "FRAN";
CREATE USER BECARIO2 IDENTIFIED BY "BECARIO2";
CREATE USER ANGEL IDENTIFIED BY "ANGEL";
CREATE USER FERNANDO IDENTIFIED BY "FERNANDO";
CREATE USER PRUEBAS IDENTIFIED BY "PRUEBAS";
CREATE USER BECARIO IDENTIFIED BY "BECARIO";
CREATE USER REMOTO IDENTIFIED BY "REMOTO";
CREATE USER USRPRACTICA2 IDENTIFIED BY "USRPRACTICA2";
CREATE USER USRPRACTICA1 IDENTIFIED BY "USRPRACTICA1";
CREATE ROLE ROLPRACTICA1;
CREATE ROLE ROLPRACTICA2;
CREATE ROLE ROLPRACTICA3;
CREATE ROLE ROLPRACTICA4;
~~~

**NOTA: Los usuarios fernando,jesus,angel y fran ya estaban creados.**

La ip de la máquina importada es:

![ip2](https://github.com/FranHuzon/Practica_Movimiento_de_datos/blob/master/images/ip2.png)

![grupal3](https://github.com/FranHuzon/Practica_Movimiento_de_datos/blob/master/images/grupal3.png)
![grupal4](https://github.com/FranHuzon/Practica_Movimiento_de_datos/blob/master/images/grupal4.png)

Comprobamos en la nueva base de datos si tenemos los datos de las tablas nuevas:

![grupal5](https://github.com/FranHuzon/Practica_Movimiento_de_datos/blob/master/images/grupal5.png)

Solamente hemos podido bajar el número de errores a 15, lo cúal es bastante aceptable. Algunos de los errores son porque ya existía el objeto que se está importando, y otros, básicamente, no hemos sido capaces de arreglarlos.


### 3. SQL\*Loader es una herramienta que sirve para cargar grandes volúmenes de datos en una instancia de ORACLE. Exportad los datos de uno de vuestros proyectos de 1º desde Postgres a texto plano con delimitadores y emplead SQL\*Loader para realizar el proceso de carga de dichos datos a una instancia ORACLE. Debéis explicar los distintos ficheros de configuración y de log que tiene SQL\*Loader.


Vamos a exportar los datos de unas tablas en una base de datos postgresql a un fichero _.dat_.

**NOTA:** Al ser el proceso muy repetitivo, se ha decidido realizar la exportación y la importación de 3 tablas.

Realizamos la exportación desde postgresql:

![grupal6](https://github.com/FranHuzon/Practica_Movimiento_de_datos/blob/master/images/grupal6.png)

Fichero usuarios.dat:

![grupal7](https://github.com/FranHuzon/Practica_Movimiento_de_datos/blob/master/images/grupal7.png)


Fichero servicios.dat:

![grupal8](https://github.com/FranHuzon/Practica_Movimiento_de_datos/blob/master/images/grupal8.png)

Fichero versionesso:

![grupal9](https://github.com/FranHuzon/Practica_Movimiento_de_datos/blob/master/images/grupal9.png)


Comprobación de los ficheros:



Ahora nos vamos a nuestro equipo con oracle, donde debemos crear un fichero de control por cada tabla. Sería algo parecido a lo siguiente:

- Fichero servicios.ctl
~~~
LOAD DATA
INSERT
INTO TABLE servicios
(
Nombre CHAR TERMINATED BY ',',
Descripcion CHAR TERMINATED BY 'chr(10)'
)
~~~
- Fichero usuarios.ctl:
~~~
LOAD DATA
INSERT
INTO TABLE usuarios
(
Nombre CHAR TERMINATED BY ',',
Contrasena CHAR TERMINATED BY ',',
NombreReal CHAR TERMINATED BY ',',
Apellidos CHAR TERMINATED BY ',',
CorreoElectronico CHAR TERMINATED BY ',',
Ciudad CHAR TERMINATED BY 'chr(10)'
)
~~~
- Fichero versionesso.ctl:
~~~
LOAD DATA
INSERT
INTO TABLE versionesso
(
Codigo CHAR TERMINATED BY ',',
Nombre CHAR TERMINATED BY 'chr(10)'
)
~~~

Realizamos la importación en la base de datos oracle:

- Tabla Servicios:

~~~
oracle@OracleJessie:~/carga_datos$ sqlldr fran/fran control=control_files/servicios.ctl data=datos/servicios.dat log=logs/servicios.log

SQL*Loader: Release 12.1.0.2.0 - Production on Jue Mar 5 20:06:38 2020

Copyright (c) 1982, 2014, Oracle and/or its affiliates.  All rights reserved.

Ruta de acceso utilizada:      Convencional
Punto de confirmación alcanzado - recuento de registros lógicos 7

Tabla SERVICIOS:
  7 Filas se ha cargado correctamente.

Consulte el archivo log:
  logs/servicios.log
para obtener más información sobre la carga.
~~~

- Tabla Usuarios:

~~~
oracle@OracleJessie:~/carga_datos$ sqlldr fran/fran control=control_files/usuarios.ctl data=datos/usuarios.dat log=logs/usuarios.log

SQL*Loader: Release 12.1.0.2.0 - Production on Jue Mar 5 20:02:50 2020

Copyright (c) 1982, 2014, Oracle and/or its affiliates.  All rights reserved.

Ruta de acceso utilizada:      Convencional
Punto de confirmación alcanzado - recuento de registros lógicos 7

Tabla USUARIOS:
  7 Filas se ha cargado correctamente.

Consulte el archivo log:
  logs/usuarios.log
para obtener más información sobre la carga.
~~~

- Tabla Versionesso:

~~~
oracle@OracleJessie:~/carga_datos$ sqlldr fran/fran control=control_files/versionesso.ctl data=datos/versionesso.dat log=logs/versionesso.log

SQL*Loader: Release 12.1.0.2.0 - Production on Jue Mar 5 20:08:58 2020

Copyright (c) 1982, 2014, Oracle and/or its affiliates.  All rights reserved.

Ruta de acceso utilizada:      Convencional
Punto de confirmación alcanzado - recuento de registros lógicos 10

Tabla VERSIONESSO:
  10 Filas se ha cargado correctamente.

Consulte el archivo log:
  logs/versionesso.log
para obtener más información sobre la carga.
~~~

Comprobamos que los datos se han insertado correctamente en las tablas:

~~~
oracle@OracleJessie:~/carga_datos$ rlwrap sqlplus fran/fran

SQL*Plus: Release 12.1.0.2.0 Production on Jue Mar 5 20:06:42 2020

Copyright (c) 1982, 2014, Oracle.  All rights reserved.

Hora de Última Conexión Correcta: Jue Mar 05 2020 20:06:38 +01:00

Conectado a:
Oracle Database 12c Enterprise Edition Release 12.1.0.2.0 - 64bit Production
With the Partitioning, OLAP, Advanced Analytics and Real Application Testing options


Sesión modificada.

SQL> select * from servicios;

NOMBRE			  | DESCRIPCION
_________________________ | ________________________________________________________________________________________________________________________
Correo                    | Presta servicio de correo electrónico entre servidores usando el protocolo SMTP.
Web			  | Recibe peticiones de un cliente y proporciona una respuesta rederizada en un navegador web usando el protocolo HTTP.
DHCP                      | Asigna direcciones IP al resto de máquinas de forma automática.
Base de datos             | Almacena\, modifica y extrae información de la base de datos.
Seguridad		  | Protege el sistema de intrusiones no deseadas.
FTP                       | Permite el desplazamiento de datos entre diferentes máquinas.
SSH                       | Controla las líneas de módem de la red para que las peticiones conecten con la red de una posición remota.

7 filas seleccionadas.

SQL> 


oracle@OracleJessie:~/carga_datos$ rlwrap sqlplus fran/fran

SQL*Plus: Release 12.1.0.2.0 Production on Jue Mar 5 20:02:54 2020

Copyright (c) 1982, 2014, Oracle.  All rights reserved.

Hora de Última Conexión Correcta: Jue Mar 05 2020 20:02:50 +01:00

Conectado a:
Oracle Database 12c Enterprise Edition Release 12.1.0.2.0 - 64bit Production
With the Partitioning, OLAP, Advanced Analytics and Real Application Testing options


Sesión modificada.

SQL> select * from usuarios;

NOMBRE		         | CONTRASENA	        | NOMBREREAL	       | APELLIDOS
____________________ | ____________________ | ____________________ | ________________________________________
CORREOELECTRONICO	 | CIUDAD
________________________________________    | ____________________
paco.garcia          | 1234567890           | Francisco            | García Fernández
pacog1@hotmail.es	 | Lagos (Portugal)

josedom24            | 1a2b3c4d5e           | Jose Domingo         | Muñoz Rodríguez
josedom24@gmail.com                         | Utrera (España)

rruizpadi            | M@ri@DB4ever         | Raúl                 | Ruiz Padilla
rruiz@gmail.com                             | Sevilla (España)

AnAcLeTo	         | b1c1clet@s	        | Rodrigo		       | Moreno Ruedas
rodrimorerue@netmail.net                    | París (Francia)

pepe perez           | 23-junio-85          | Juan                 | Pérez Pérez
eldeejemplo@orgmail.org 		            | Portimao (Portugal)

juana_la_loca        | felipe_hermoso_mio   | Juana                | Princesa Católica
juana@edumail.edu                           | Aragón (España)

Rosa_FOL             | @@@ROSA@@@           | Rosa                 | López de España
eurovision@infomail.info                    | Teruel (España)


7 filas seleccionadas.

SQL>


oracle@OracleJessie:~/carga_datos$ rlwrap sqlplus fran/fran

SQL*Plus: Release 12.1.0.2.0 Production on Jue Mar 5 20:09:06 2020

Copyright (c) 1982, 2014, Oracle.  All rights reserved.

Hora de Última Conexión Correcta: Jue Mar 05 2020 20:08:58 +01:00

Conectado a:
Oracle Database 12c Enterprise Edition Release 12.1.0.2.0 - 64bit Production
With the Partitioning, OLAP, Advanced Analytics and Real Application Testing options

Sesión modificada.

SQL> select * from versionesso;

CODIGO		| NOMBRE
_______________ | ____________________
Debian9.0	| Debian Stretch
Debian8.0	| Debian Jessie
WXP		| Windows Xp
W7		| Windows Siete
W10		| Windows Diez
WS2003		| Windows Server
WS2012		| Windows Server
WS2016		| Windows Server
Fedora22	| Fedora
CentOS7 	| Cent Os

10 filas seleccionadas.

SQL> 
~~~



### controlfile
Respecto a los ficheros de configuración todos tienen la misma estructura y es la siguiente:

- LOAD DATA: Indica el comienzo de la declaración de inserción de datos.
- INSERT: Indica la acción a realizar, en nuestro caso la sentencia INSERT.
- INTO TABLE: Se especifica los campos que va a tener la tabla.
- TERMINATED BY: Indica el carácter delimitador para definir los cmapos de la tabla.

### datafile

Contiene los datos a importar de la tabla que previamente se ha exportado.

### logfile
Estos ficheros muestran información de las acciones que se han ido haciendo durante el proceso de importación, como por ejemplo, nombre del controlfile, nombre del datafile, la estructura de la tabla importada, número de errores producidos y su causa, el tiempo que ha tardado en realizar la acción, así como otros datos.

Aquí tenemos los 3 ficheros de logs que hemos generado:

~~~
## logs/servicios.log
SQL*Loader: Release 12.1.0.2.0 - Production on Jue Mar 5 20:06:38 2020
Copyright (c) 1982, 2014, Oracle and/or its affiliates.  All rights reserved.
Archivo de Control:   control_files/servicios.ctl
Archivo de Datos:      datos/servicios.dat
  Archivo de Errores:     control_files/servicios.bad
  Desechar Archivo: exportacion_postgre.bad 
 (Permitir todos los registros desechados)
Número a cargar: ALL
Número que omitir: 0
Errores permitidos: 50
Matriz de enlace:     64 filas, máximo de 256000 bytes
Continuación:    ninguno especificado
Ruta de acceso utilizada:      Convencional
Tabla SERVICIOS, cargada de cada registro lógico.
Opción INSERT activa para esta tabla: INSERT
   Nombre Columna                  Posición   Long  Term Entorno Tipo de Dato
------------------------------ ---------- ----- ---- ---- ---------------------
NOMBRE                              FIRST     *   ,       CHARACTER            
DESCRIPCION                          NEXT     *           CHARACTER            
    Cadena de terminador: 'chr(10)'
Tabla SERVICIOS:
  7 Filas se ha cargado correctamente.
  0 Filas no cargada debido a errores de datos.
  0 Filas no cargada porque todas las cláusulas WHEN han fallado.
  0 Filas no cargada porque todos los campos eran nulos.
Espacio asignado a matriz de enlace:            33024 bytes (64 filas)
Bytes de buffer de lectura: 1048576
Total de registros lógicos ignorados:          0
Total de registros lógicos leídos:           7
Total de registros lógicos rechazados:         0
Total de registros lógicos desechados:        0
La ejecución empezó en Jue Mar 05 20:06:38 2020
La ejecución terminó en Jue Mar 05 20:06:38 2020
Tiempo transcurrido:     00:00:00.05
Tiempo de CPU:         00:00:00.02


## logs/usuarios.log
SQL*Loader: Release 12.1.0.2.0 - Production on Jue Mar 5 20:02:50 2020
Copyright (c) 1982, 2014, Oracle and/or its affiliates.  All rights reserved.
Archivo de Control:   control_files/usuarios.ctl
Archivo de Datos:      datos/usuarios.dat
  Archivo de Errores:     control_files/usuarios.bad
  Desechar Archivo: exportacion_postgre.bad 
 (Permitir todos los registros desechados)
Número a cargar: ALL
Número que omitir: 0
Errores permitidos: 50
Matriz de enlace:     64 filas, máximo de 256000 bytes
Continuación:    ninguno especificado
Ruta de acceso utilizada:      Convencional
Tabla USUARIOS, cargada de cada registro lógico.
Opción INSERT activa para esta tabla: INSERT
   Nombre Columna                  Posición   Long  Term Entorno Tipo de Dato
------------------------------ ---------- ----- ---- ---- ---------------------
NOMBRE                              FIRST     *   ,       CHARACTER            
CONTRASENA                           NEXT     *   ,       CHARACTER            
NOMBREREAL                           NEXT     *   ,       CHARACTER            
APELLIDOS                            NEXT     *   ,       CHARACTER            
CORREOELECTRONICO                    NEXT     *   ,       CHARACTER            
CIUDAD                               NEXT     *           CHARACTER            
    Cadena de terminador: 'chr(10)'
Tabla USUARIOS:
  7 Filas se ha cargado correctamente.
  0 Filas no cargada debido a errores de datos.
  0 Filas no cargada porque todas las cláusulas WHEN han fallado.
  0 Filas no cargada porque todos los campos eran nulos.
Espacio asignado a matriz de enlace:            99072 bytes (64 filas)
Bytes de buffer de lectura: 1048576
Total de registros lógicos ignorados:          0
Total de registros lógicos leídos:           7
Total de registros lógicos rechazados:         0
Total de registros lógicos desechados:        0
La ejecución empezó en Jue Mar 05 20:02:50 2020
La ejecución terminó en Jue Mar 05 20:02:50 2020
Tiempo transcurrido:     00:00:00.09
Tiempo de CPU:         00:00:00.03

## logs/versionesso.log
SQL*Loader: Release 12.1.0.2.0 - Production on Jue Mar 5 20:08:58 2020
Copyright (c) 1982, 2014, Oracle and/or its affiliates.  All rights reserved.
Archivo de Control:   control_files/versionesso.ctl
Archivo de Datos:      datos/versionesso.dat
  Archivo de Errores:     control_files/versionesso.bad
  Desechar Archivo: exportacion_postgre.bad 
 (Permitir todos los registros desechados)
Número a cargar: ALL
Número que omitir: 0
Errores permitidos: 50
Matriz de enlace:     64 filas, máximo de 256000 bytes
Continuación:    ninguno especificado
Ruta de acceso utilizada:      Convencional
Tabla VERSIONESSO, cargada de cada registro lógico.
Opción INSERT activa para esta tabla: INSERT
   Nombre Columna                  Posición   Long  Term Entorno Tipo de Dato
------------------------------ ---------- ----- ---- ---- ---------------------
CODIGO                              FIRST     *   ,       CHARACTER            
NOMBRE                               NEXT     *           CHARACTER            
    Cadena de terminador: 'chr(10)'
Tabla VERSIONESSO:
  10 Filas se ha cargado correctamente.
  0 Filas no cargada debido a errores de datos.
  0 Filas no cargada porque todas las cláusulas WHEN han fallado.
  0 Filas no cargada porque todos los campos eran nulos.
Espacio asignado a matriz de enlace:            33024 bytes (64 filas)
Bytes de buffer de lectura: 1048576
Total de registros lógicos ignorados:          0
Total de registros lógicos leídos:          10
Total de registros lógicos rechazados:         0
Total de registros lógicos desechados:        0
La ejecución empezó en Jue Mar 05 20:08:58 2020
La ejecución terminó en Jue Mar 05 20:08:58 2020
Tiempo transcurrido:     00:00:00.07
Tiempo de CPU:         00:00:00.02
~~~

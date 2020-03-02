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

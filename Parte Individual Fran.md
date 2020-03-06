### 1. Realiza una exportación del esquema de SCOTT usando la consola Enterprise Manager, con las siguientes condiciones:

- Exporta tanto la estructura de las tablas como los datos de las mismas.
- Excluye la tabla SALGRADE y los departamentos que no tienen empleados.
- Programa la operación para dentro de 15 minutos.
- Genera un archivo de log en el directorio raíz de ORACLE.

Realiza ahora la operación con Oracle Data Pump.

**NOTA:** Al no funcionarme la consola Enterprise Manager, he optado por realizar todo el ejercicio con Oracle Data Pump.

Creamos un directorio para guardar la exportación y damos permiso de lectura y escritura al usuario sobre el directorio:

~~~
SQL> CREATE OR REPLACE DIRECTORY test_dir AS '/opt/oracle/oradata/';

Directorio creado.

SQL> GRANT READ, WRITE ON DIRECTORY test_dir TO SYSTEM;

Concesión terminada correctamente.

SQL> 
~~~

Exportamos la estructura y los datos de las tablas de SCOTT:

[1](https://github.com/FranHuzon/Practica_Movimiento_de_datos/blob/master/images/1.png)

[2](https://github.com/FranHuzon/Practica_Movimiento_de_datos/blob/master/images/2.png)

Vamos a realizar otra vez la exportación la exportación pero excluyendo la tabla SALGRADE y los departamentos sin empleados. Para ello usamos la opción _exclude_ y la opción _query_:

[3](https://github.com/FranHuzon/Practica_Movimiento_de_datos/blob/master/images/3.png)

[4](https://github.com/FranHuzon/Practica_Movimiento_de_datos/blob/master/images/4.png)


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

![1](https://github.com/FranHuzon/Practica_Movimiento_de_datos/blob/master/images/1.png)

![2](https://github.com/FranHuzon/Practica_Movimiento_de_datos/blob/master/images/2.png)

Vamos a realizar otra vez la exportación la exportación pero excluyendo la tabla SALGRADE y los departamentos sin empleados. Para ello usamos la opción _exclude_ y la opción _query_:

![3](https://github.com/FranHuzon/Practica_Movimiento_de_datos/blob/master/images/3.png)

![4](https://github.com/FranHuzon/Practica_Movimiento_de_datos/blob/master/images/4.png)

Para programar la exportación he optado por una tarea crontab muy sencilla. Creamos un scrip con el siguiente contenido:

~~~
#!/bin/bash

export ORACLE_BASE=/opt/oracle
export ORACLE_HOME=/opt/oracle/product/12.1.0.2/dbhome_1
export ORACLE_SID=orasid
export PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/opt/oracle/product/12.1.0.2/dbhome_1/bin
expdp SYSTEM/RAUL@orcl schemas=SCOTT directory=TEST_DIR dumpfile=exp-programada.dmp logfile=log-programada.log
~~~

Le damos permiso de ejecución:

~~~
chmod u+x /opt/oracle/oradata/programada.sh
~~~

Utilizamos el comando _crontab -e_ y añadimos al final del fichero:

~~~
15 * * * * /opt/oracle/oradata/programada.sh
~~~

Esto nos realizará dicha tarea cada 15 minutos. Pasados los 15 minutos nos llega un correo a /var/mail/oracle con la información sibre la tarea ejecutada. Vamos a comprobar si ha funcionado visualizando el correo y viendo los ficheros en el directo:

![5](https://github.com/FranHuzon/Practica_Movimiento_de_datos/blob/master/images/5.png)

Para generar un fichero de log solo hay que usar la opción _logfile=_ con el comando _expdp_. En los ejercicios anteriores hemos ido exportando un fichero de log junto al fichero de la exportación, por eso no voy a adjuntar otro ejemplo.

### 4. Intenta realizar operaciones similares de importación y exportación con las herramientas proporcionadas con Postgres desde línea de comandos, documentando el proceso.

Partimos de una base de datos postgresql con un usuario 'fran' y una base de datos 'proyecto' con tabla 'ordenadores'.

Realizamos la exportación con la herramienta _pg\_dump_:

![9-2](https://github.com/FranHuzon/Practica_Movimiento_de_datos/blob/master/images/9-2.png)

Contenido del fichero _exp\_ordenadores.sql_:

~~~
postgres@oracle:~$ cat exp_ordenadores.sql 
--
-- PostgreSQL database dump
--

SET statement_timeout = 0;
SET lock_timeout = 0;
SET client_encoding = 'UTF8';
SET standard_conforming_strings = on;
SELECT pg_catalog.set_config('search_path', '', false);
SET check_function_bodies = false;
SET xmloption = content;
SET client_min_messages = warning;

SET default_tablespace = '';

SET default_with_oids = false;

--
-- Name: ordenadores; Type: TABLE; Schema: public; Owner: fran; Tablespace: 
--

CREATE TABLE public.ordenadores (
    numeroserie character varying(15) NOT NULL,
    CONSTRAINT formatonumserie CHECK (((numeroserie)::text ~ '^[A-Za-z]{2}-[0-9]{5}-[A-Za-z]-[0-9]{2}$'::text))
);


ALTER TABLE public.ordenadores OWNER TO fran;

--
-- Data for Name: ordenadores; Type: TABLE DATA; Schema: public; Owner: fran
--

COPY public.ordenadores (numeroserie) FROM stdin;
SS-00001-S-01
SS-00002-S-02
SS-00003-S-03
SS-00004-S-04
SS-00005-S-05
SS-00006-S-06
AA-00001-A-01
AA-00002-A-02
AA-00003-A-03
DD-00001-D-01
DD-00002-D-02
DD-00003-D-03
\.


--
-- Name: pk_ordenadores; Type: CONSTRAINT; Schema: public; Owner: fran; Tablespace: 
--

ALTER TABLE ONLY public.ordenadores
    ADD CONSTRAINT pk_ordenadores PRIMARY KEY (numeroserie);


--
-- PostgreSQL database dump complete
--
~~~

Ahora vamos a realizar una importación. Conectamos a psql con el usuario postgres:

![6](https://github.com/FranHuzon/Practica_Movimiento_de_datos/blob/master/images/6.png)

![7](https://github.com/FranHuzon/Practica_Movimiento_de_datos/blob/master/images/7.png)

![8](https://github.com/FranHuzon/Practica_Movimiento_de_datos/blob/master/images/8.png)

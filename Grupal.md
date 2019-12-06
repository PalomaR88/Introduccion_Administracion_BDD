# Parte individual

## Alumno 1 - Adrián Jaramillo Rodríguez
[ORACLE](https://github.com/adriasir123/individual_p3_bd1920)

## Alumno 2 - Paloma R. García Campón 
[Postgres y ORACLE](https://github.com/PalomaR88/Introduccion_Administracion_BDD/blob/master/Practica.md)


## Alumno 3 - José María Montero Romero
[MySQL](https://github.com/josemariamontero/usuariosbd/blob/master/practicaindividual)
[ORACLE](https://github.com/josemariamontero/usuariosbd/blob/master/procedimientos)

## Alumno 4 - Juan Diego Abadía Noguera
[MongoDB y ORACLE](https://github.com/JuandiNoguera/Practica3GBD/blob/master/Individual.md)



# Parte grupal

## CASO PRÁCTICO 1      

### 1. (ORACLE, Postgres, MySQL) Crea un usuario llamado Becario y, sin usar los roles de ORACLE, dale los siguientes privilegios:

**ORACLE**

~~~
CREATE USER becario IDENTIFIED BY becario;
~~~

**POSTGRES**

Creación del usuario con la opción contraseña:
~~~
CREATE USER becario WITH PASSWORD 'becario';
~~~

Creación de una base de datos cuyo propietario es becario:
~~~
CREATE database bdbecario;
GRANT connect ON DATABASE bdbecario TO becario;
~~~


#### - Conectarse a la base de datos.

**ORACLE**
~~~
GRANT CREATE SESSION TO becario;

Prueba:

SQL> connect becario

Enter password:

Connected.

SQL>
~~~


**POSTGRES**

Al crear el "usuario"/"rol" con CREATE USER se agrega la opción login. Pero si el usuario se ha creado con CREATE ROL habría que añadir dicha opción para que pueda crear sesiones:
~~~
ALTER ROLE becario WITH login;
~~~

Además, hay que modificar el fichero /etc/postgresql/11/main/pg_hba.conf de tal forma que la opción para el método de loguearse en local sea md5:
~~~
local   all             all                                     md5 
~~~


Y se inicia sesión con las opciones -U (usuario):
~~~
psql -U <usuario> -d <base_datos> -h <direccion_host> -W
~~~

~~~
postgres@servidor:~$ psql -U becario -d bdbecario -h localhost -W
Password: 
psql (11.5 (Debian 11.5-1+deb10u1))
SSL connection (protocol: TLSv1.3, cipher: TLS_AES_256_GCM_SHA384, bits: 256, compression: off)
ario: 
psql (11.5 (Debian 11.5-1+deb10u1))
Type "help" for help.

bdbecario=> 
~~~

**MARIADB**
~~~
grant usage on *.* to becario identified by 'becario';
~~~


#### - Modificar el número de errores en la introducción de la contraseña de cualquier usuario.

**ORACLE**
Bloquear:
 Tenemos 1 intento fallido, y bloquea cuando llegamos a ese límite de intentos (pero avisa al siguiente intento de login)

~~~
ALTER PROFILE default LIMIT

​    FAILED_LOGIN_ATTEMPTS 1;
~~~
  
  Desbloquear cuenta bloqueada:
~~~
ALTER USER becario IDENTIFIED BY becario ACCOUNT UNLOCK;
~~~

Prueba
~~~
SQL> connect becario

Enter password:

ERROR:

ORA-01017: invalid username/password; logon denied





SQL> connect becario

Enter password:

ERROR:

ORA-28000: the account is locked
~~~



#### - Modificar índices en cualquier esquema (este privilegio podrá pasarlo a quien quiera)

**ORACLE**
~~~
   GRANT ALTER ANY INDEX TO becario with admin option;

SQL> select * from all_ind_columns where table_name = 'EMP';



INDEX_OWNER          INDEX_NAME

------------------------------ ------------------------------

TABLE_OWNER          TABLE_NAME

------------------------------ ------------------------------

COLUMN_NAME

\--------------------------------------------------------------------------------

COLUMN_POSITION COLUMN_LENGTH CHAR_LENGTH DESC

--------------- ------------- ----------- ----

SCOTT             PK_EMP

SCOTT             EMP

EMPNO

​       1      22      0 ASC
~~~

**POSTGRES**
Postgres no tiene opción para asignar un privilegio que permita modificar cualquier indice. 


**MARIADB**
~~~
grant alter,index on *.* to becario identified by 'becario' with grant option;
~~~


#### - Insertar filas en scott.emp (este privilegio podrá pasarlo a quien quiera)

**ORACLE**
~~~
grant INSERT on SCOTT.EMP to becario with grant option;
~~~

Prueba:
~~~
SQL> connect becario
Enter password:
Connected.
SQL> insert into scott.emp (empno, ename) values('8000', 'ejemplo');

1 row created.

~~~


Datos mostrados para que se vea que se ha insertado
~~~
     EMPNO ENAME      JOB              MGR HIREDATE        SAL       COMM
---------- ---------- --------- ---------- -------- ---------- ----------
    DEPTNO
----------
      7902 FORD       ANALYST         7566 03/12/81       3000
        20

      7934 MILLER     CLERK           7782 23/01/82       1300
        10

      8000 ejemplo
~~~

**POSTGRES**
~~~
GRANT INSERT ON scott.EMP to becario WITH GRANT OPTION;
~~~


**MARIADB**
~~~
grant insert on SCOTT.emp to becario identified by 'becario' with grant option;
~~~

#### - Crear objetos en cualquier tablespace.

**ORACLE**
~~~
GRANT UNLIMITED TABLESPACE TO becario;
select tablespace_name from user_tablespaces;
~~~

**POSTGRES**
En Postgres se puede dar permisos sobre un tablespace concreto con la siguiente sintaxis:
~~~
GRANT CREATE ON TABLESPACE <nombre_tablespace> TO becario;
~~~

**MARIADB**
~~~
grant create tablespace on *.* to becario identified by 'becario';
~~~


#### - Gestión completa de usuarios, privilegios y roles.
**ORACLE**

USUARIOS

Asignación de los privilegios sobre la gestión de usuario:
~~~
grant create user to becario;
grant alter user to becario;
grant drop user to becario;
~~~

Pruebas de funcionamiento
~~~
Introduzca el nombre de usuario: becario
Introduzca la contrase±a:

Conectado a:
Oracle Database 11g Enterprise Edition Release 11.2.0.1.0 - 64bit Production
With the Partitioning, OLAP, Data Mining and Real Application Testing options

SQL> create user prueba identified by prueba;

Usuario creado.

SQL> alter user prueba identified by password;

Usuario modificado.

SQL> drop user prueba;

Usuario borrado.
~~~

PRIVILEGIOS

Asignación de los privilegios sobre la gestión de privilegios:
~~~
grant grant any privilege to becario;
grant grant any object privilege to becario;
~~~

Pruebas de funcionamiento
~~~
SQL> grant SELECT on SCOTT.EMP to prueba;

Concesi¾n terminada correctamente.

SQL> grant CREATE ANY TABLE to prueba;

Concesi¾n terminada correctamente.
~~~

ROLES

Asignación de los privilegios sobre la gestión de roles:
~~~
grant create role to becario;
grant alter any role to becario;
grant drop any role to becario;
~~~

Prueba de funcionamiento
~~~
SQL> create role becarios;

Rol creado.

SQL> alter role becarios identified globally;

Rol modificado.

SQL> drop role becarios;

Rol borrado.
~~~

**POSTGRES**

Hay que asignar al rol la opción de superuser:
~~~
ALTER ROLE becario WITH SUPERUSER;
~~~

Pero esta opción otorga más permisos de los que se pide en el enunciado. Por lo tanto, lo correcto sería otorgarle los privilegios específicos que serían:

Para administrar roles:
~~~
ALTER ROLE becario WITH CREATEROLE;
~~~

Para otorgar el privilegio de login y de crear bases de datos a otros usuarios hay que añadir las siguientes opciones al usuario:
~~~
ALTER ROLE becario WITH LOGIN WITH GRANT OPTION;
ALTER ROLE becario WITH CREATEDB WITH GRANT OPTION;
~~~

**MARIADB**
~~~
grant select,update,insert on *.* to becario identified by 'becario';
grant create on *.* to becario identified by 'becario';
grant drop on *.* to becario identified by 'becario';
grant alter on *.* to becario identified by 'becario';
grant create user on *.* to becario identified by 'becario';
~~~


### EJERCICIO 2 (ORACLE, Postgres, MySQL) Escribe una consulta que obtenga un script para quitar el privilegio de borrar registros en alguna tabla de SCOTT a los usuarios que lo tengan.

**ORACLE**
~~~
select 'revoke delete on SCOTT.'||table_name||' from ' || grantee ||';'
from DBA_TAB_PRIVS
where owner='SCOTT'
and privilege='DELETE';
~~~

**POSTGRES**
En Postgres no se puede indicar el usuario propietario de las tablas. Por el contrario, permite filtrar las tablas por la base de datos o/y el esquema a la que pertenece.
~~~
select 'REVOKE DELETE ON '||table_catalog||'.'||table_name||' from '|| grantee||';'
from information_schema.role_table_grants
where privilege_type='DELETE'
[and table_catalog='SCOTT']
[and table_schema='SCOTT'];
~~~

**MARIADB**
~~~
select 'REVOKE DELETE on SCOTT.*' || table_name || ' from ' || grantee || ';'
from table_privileges
where grantee = 'SCOTT'
and privilege_type = 'DELETE';
~~~


### EJERCICIO 3 (ORACLE) Crea un tablespace TS2 con tamaño de extensión de 256K. 
Realiza una consulta que genere un script que:
#### - Asigne ese tablespace como tablespace por defecto a los usuarios que no tienen privilegios para consultar ninguna tabla de SCOTT, excepto a SYSTEM (a system no hay que asignarle el tablespace)

Crear el tablespace
~~~
CREATE TABLESPACE TS2 
   DATAFILE 'tbs_ts2.dbf' 
   SIZE 256k;
~~~

Hacer la consulta
~~~
select 'alter user "'||username||'" default tablespace TS2;'
from dba_users
where username != 'SYSTEM'
and username NOT IN (select grantee from dba_tab_privs where privilege = 'SELECT' AND owner = 'SCOTT');
~~~



### EJERCICIO 4 (ORACLE, Postgres) Realiza un procedimiento que reciba un nombre de usuario y nos muestre cuántas sesiones tiene abiertas en este momento. Además, para cada una de dichas sesiones nos mostrará la hora de comienzo y el nombre de la máquina, sistema operativo y programa desde el que fue abierta.

**ORACLE**
~~~
create or replace procedure MostrarSesiones(p_user varchar2)
is
    v_numero number:=0;
    cursor c_sesiones
    is
    select to_char(logon_time, 'HH24:MI') as hora, machine, program
    from v$session
    where username=p_user;
    v_cursor c_sesiones%ROWTYPE;
begin
    for v_cursor in c_sesiones loop
        dbms_output.put_line('Hora: '||v_cursor.hora||'Maquina: '||v_cursor.machine||'Programa: '||v_cursor.program);
    v_numero:=v_numero+1;
    end loop;
    dbms_output.put_line('Numero de sesiones abiertas: '||v_numero);
end MostrarSesiones;
/

exec MostrarSesiones('SYS');
~~~

**POSTGRES**
~~~
create or replace function MostrarSesiones (p_user varchar)
returns void as $$
declare
    v_cont NUMERIC:=0;
    c_sesiones cursor is
	SELECT to_char(backend_start,'DD/MM/YYYY HH24:MI') hora, client_hostname, application_name
	from pg_stat_activity
	where usename=lower(p_user);
begin
    for v_cursor in c_sesiones loop
        RAISE NOTICE 'Hora: %', v_cursor.hora;
        RAISE NOTICE 'Maquina: %', v_cursor.client_hostname;
        RAISE NOTICE 'Programa: %',v_cursor.application_name;
        RAISE NOTICE ' ';
        v_cont:=v_cont+1;
    end loop;
    RAISE NOTICE 'Numero de sesiones abiertas: %', v_cont;
end;
$$ LANGUAGE plpgsql;

select MostrarSesiones('postgres');
~~~


### 5. (ORACLE) Realiza un procedimiento que muestre los usuarios que pueden conceder privilegios de sistema a otros usuarios y cuales son dichos privilegios.
~~~
create or replace procedure PrivilegiosDeAdmin
is
    cursor c_cursor
    is
    select grantee, privilege
    from DBA_SYS_PRIVS 
    where admin_option='YES';
    v_cursor c_cursor%ROWTYPE;
begin
    for v_cursor in c_cursor loop
        dbms_output.put_line('Usuario: '||v_cursor.grantee||' - '||'Privilegio: '||v_cursor.privilege);
    end loop;
end PrivilegiosDeAdmin;
/

exec PrivilegiosDeAdmin
~~~


## CASO PRÁCTICO 2

### 1. (ORACLE) La vida de un DBA es dura. Tras pedirlo insistentemente, en tu empresa han contratado una persona para ayudarte. Decides que se encargará de las siguientes tareas:

#### - Resetear los archivos de log en caso de necesidad.

Para saber donde se encuentran los ficheros de log y es estado:
~~~
SQL> archive log list
Modo log de la base de datos		  Modo de No Archivado
Archivado automático             Desactivado
Destino del archivo	       /opt/oracle/product/12.2.0.1/dbhome_1/dbs/arch
Secuencia de log en línea más antigua     19
Secuencia de log actual 	  21
~~~

~~~
GRANT ALTER DATABASE to ayudante;
~~~

~~~
SQL> ALTER DATABASE CLEAR LOGFILE GROUP 1;

Base de datos modificada.

SQL> archive log list
Modo log de la base de datos		  Modo de No Archivado
Archivado automático             Desactivado
Destino del archivo	       /opt/oracle/product/12.2.0.1/dbhome_1/dbs/arch
Secuencia de log en línea más antigua     0
Secuencia de log actual 	  21
~~~

#### - Crear funciones de complejidad de contraseña y asignárselas a usuarios.

~~~
GRANT CREATE ANY PROCEDURE TO ayudante;
GRANT ALTER PROFILE TO ayudante;

~~~

#### - Eliminar la información de rollback. (este privilegio podrá pasarlo a quien quiera)

~~~
GRANT DROP ROLLBACK SEGMENT TO ayudante with grant option;
~~~

#### - Modificar información existente en la tabla dept del usuario scott. (este privilegio podrá pasarlo a quien quiera)

~~~
GRANT UPDATE on SCOTT.DEPT to ayudante with grant option;
GRANT SELECT on SCOTT.DEPT to ayudante;
~~~

#### - Modificacion de la localización del departamento 40
~~~
SQL> update scott.dept set LOC='MADRID'
  2  where DEPTNO=40;

1 fila actualizada.

SQL> select * from scott.dept;

    DEPTNO DNAME          LOC
---------- -------------- -------------
        10 ACCOUNTING     NEW YORK
        20 RESEARCH       DALLAS
        30 SALES          CHICAGO
        40 OPERATIONS     MADRID
~~~


#### - Realizar pruebas de todos los procedimientos existentes en la base de datos.

~~~
GRANT EXECUTE ANY PROCEDURE to ayudante;
~~~

#### - Poner un tablespace fuera de línea.

~~~
GRANT ALTER TABLESPACE to ayudante;
~~~

#### - Como sysdba hemos creado un tablespace
~~~
CREATE TABLESPACE prueba
  DATAFILE 'prueba.dat' 
    SIZE 20M
  ONLINE;
~~~

#### - Como ayudante lo modificamos
~~~
SQL> alter tablespace prueba offline;

Tablespace modificado.
~~~

#### Crea un usuario llamado Ayudante y, sin usar los roles predefinidos de ORACLE, dale  los privilegios mínimos para que pueda resolver dichas tareas.

* CREAR USUARIO
~~~
CREATE USER ayudante IDENTIFIED BY ayudante;
~~~


### 2. (ORACLE) Muestra el texto de la última sentencia SQL que se ejecutó en el servidor, junto con el número de veces que se ha ejecutado desde que se cargó en el Shared Pool y el tiempo de CPU empleado en su ejecución.

Los datos referentes a las ejecuciones SQL se guardan en la vista `gv$sql` (la g indica que es una vista global).
Todo lo que vayamos a usar en este ejercicio, es sobre la vista `gv$sql`. La documentación que contiene toda la información sobre ésta, está [aquí](https://docs.oracle.com/cd/B19306_01/server.102/b14237/dynviews_2113.htm#REFRN30246)


- Texto de la última sentencia SQL que se ejecutó en el servidor
SQL_TEXT: muestra los primeros 1000 caracteres de la consulta
SQL_FULLTEXT: muestra la consulta completa como tipo de dato CLOB


- Nº veces que se ha ejecutado desde que se cargó en el Shared Pool
EXECUTIONS: número de veces que se ejecutó la consulta


- Tiempo de CPU empleado en su ejecución
CPU_TIME: tiempo en microsegundos que empleó de CPU para ejecutar la consulta


**Consulta completa a ejecutar**
```
SELECT sq.SQL_TEXT ,sq.EXECUTIONS ,sq.CPU_TIME, sq.LAST_ACTIVE_TIME
    FROM gv$sql sq
    ORDER BY sq.LAST_ACTIVE_TIME DESC
    FETCH NEXT 1 ROWS ONLY;
```
**Output del comando**
```
SQL_TEXT
------------------------------------------------------------------------------------------------------
------------------------------------------------------------------------------------------------------
------------------------------------------------------------------------------------------------------
----------------------------------------------------------------------------------------------------
EXECUTIONS   CPU_TIME LAST_ACT
---------- ---------- --------
select o.owner#,o.name,o.namespace,o.remoteowner,o.linkname,o.subname from obj$ o where o.obj#=:1
     50843     375000 06/12/19

```


> NOTAS: hemos ejecutado la consulta desde system con permisos de sysdba, por lo tanto la última consulta que se ha ejecutado en el servidor puede ser cualquiera de las que hayan hecho los procesos internos de oracle. Por eso es que hemos obtenido una consulta que nosotros en ningún momento hicimos.
> PERO, eso sí, es la ÚLTIMA consulta que se ha ejecutado en el servidor, porque hemos ordenado por la última vez que estuvieron activas las consultas descendentemente.



### 3. (ORACLE, Postgres) Realiza un procedimiento que reciba dos nombres de usuario y genere un script que asigne al primero los privilegios de inserción y modificación sobre todas las tablas del segundo, así como el de ejecución de cualquier procedimiento que tenga el segundo usuario.

**ORACLE**
~~~
create or replace procedure OtorgarPrivilegios(p_user1 varchar2,
                                               p_user2 varchar2)
is
    v_validacion1 number:=0;
    v_validacion2 number:=0;
begin
    v_validacion1:=ComprobarUsuario(p_user1);
    v_validacion2:=ComprobarUsuario(p_user2);
    if v_validacion1=0 and v_validacion2=0 then
        PrivilegiosSobreTablas(p_user1, p_user2);
        PrivilegiosSobreProcedimientos(p_user1, p_user2);
    end if;
end OtorgarPrivilegios;
/

create or replace function ComprobarUsuario(p_user varchar2)
return number
is
    v_resultado varchar2(30);
begin
    select USERNAME into v_resultado
    from DBA_USERS
    where username=p_user;
    return 0;
exception
    when NO_DATA_FOUND then
        dbms_output.put_line('No existe el usuario '||p_user);
        return 1;
end ComprobarUsuario;
/


create or replace procedure PrivilegiosSobreTablas(p_user1 varchar2, 
                                                   p_user2 varchar2)
is
    cursor c_tablas
    is
    select OBJECT_NAME
    from DBA_OBJECTS
    where OBJECT_TYPE='TABLE'
    and owner=p_user2;
    v_cursor c_tablas%ROWTYPE;
begin
    for v_cursor in c_tablas loop
        dbms_output.put_line('GRANT INSERT ON '||p_user2||'.'||v_cursor.OBJECT_NAME||' TO '||p_user1||';');
        dbms_output.put_line('GRANT UPDATE ON '||p_user2||'.'||v_cursor.OBJECT_NAME||' TO '||p_user1||';');
    end loop;
end PrivilegiosSobreTablas;
/

create or replace procedure PrivilegiosSobreProcedimientos (p_user1 varchar2, 
                                                            p_user2 varchar2)
is
    cursor c_procedimientos
    is
    select OBJECT_NAME
    from DBA_OBJECTS
    where OBJECT_TYPE='PROCEDURE'
    and owner=p_user2;
    v_cursor c_procedimientos%ROWTYPE;  
begin
    for v_cursor in c_procedimientos loop
        dbms_output.put_line('GRANT EXECUTE ON '||p_user2||'.'||v_cursor.OBJECT_NAME||' TO '||p_user1||';');
    end loop;
end PrivilegiosSobreProcedimientos;
/
~~~

POSTGRES
En Postgres los usuarios no son propietarios de los objetos sino de bases de datos y esquemas. Y los objetos forman parte de los esquemas. Se ha intentado realizar este ejercicio filtrando los esquemas que pertenecen a un usuario concreto, pero solo se ha podido listar los esquemas con sus respectivos propietarios con \dn. Pero, ante la imposibilidad de filtrar la opción \dn con una select, se ha llegado a la conclusión de que no se puede realizar este ejercicio en este gestor. 





4. (ORACLE) Realiza un procedimiento que genere un script que cree un rol conteniendo todos los permisos que tenga el usuario cuyo nombre reciba como parámetro, le hayan sido asignados a aquél directamente o a traves de roles. El nuevo rol deberá llamarse BackupPrivsNombreUsuario.

> Para este procedimiento se va a usar una función del apartado 3.
~~~
create or replace procedure CrearRol (p_user varchar2)
is
    v_validacion number:=0;
    v_nuevoRol varchar(50):='BackupPrivs'||p_user;
begin
    v_validacion:=ComprobarUsuario(p_user);
    if v_validacion=0 then
        dbms_output.put_line('CREATE ROLE BackupPrivs'||p_user);
        PrivilegiosDelSistema(p_user, v_nuevoRol);
        PrivilegiosSobreObjetos(p_user, v_nuevoRol);
    end if;
end CrearRol;
/

exec CrearRol ('SYS')


create or replace procedure PrivilegiosSobreObjetos(p_user varchar2, 
                                                    p_nuevoRol varchar2)
is
    cursor c_privTab
    is
    select distinct PRIVILEGE, OWNER, TABLE_NAME
    from DBA_TAB_PRIVS
    where GRANTEE=p_user
    or GRANTEE in (select distinct granted_role
                   from DBA_ROLE_PRIVS
                   start with grantee = p_user
                   connect by grantee = prior granted_role);
    v_cursor c_privTab%ROWTYPE;
begin
    for v_cursor in c_privTab loop
        AgregarPrivilegioSobreObjeto(v_cursor.PRIVILEGE, v_cursor.OWNER, v_cursor.TABLE_NAME, p_nuevoRol);
    end loop;
end PrivilegiosSobreObjetos;
/



create or replace procedure AgregarPrivilegioSobreObjeto(p_privilegio DBA_TAB_PRIVS.PRIVILEGE%TYPE, 
                                                         p_propietario DBA_TAB_PRIVS.OWNER%TYPE, 
                                                         p_nombreTabla DBA_TAB_PRIVS.TABLE_NAME%TYPE, 
                                                         p_nuevoRol varchar2)
is
begin
    dbms_output.put_line('GRANT '||p_privilegio||' ON '||p_propietario||'.'||p_nombreTabla||' TO '||p_nuevoRol||';');
end AgregarPrivilegioSobreObjeto;
/



create or replace procedure PrivilegiosDelSistema(p_user varchar2,
                                                  p_nuevoRol varchar2)
is
    cursor c_privSis
    is
    select distinct PRIVILEGE, ADMIN_OPTION
    from DBA_SYS_PRIVS
    where GRANTEE=p_user
    or GRANTEE in (select distinct granted_role
                   from DBA_ROLE_PRIVS
                   start with grantee = p_user
                   connect by grantee = prior granted_role);
    v_cursor c_privSis%ROWTYPE;
begin
    for v_cursor in c_privSis loop
        AgregarPrivilegioDelSistema(v_cursor.PRIVILEGE, v_cursor.ADMIN_OPTION, p_nuevoRol);
    end loop;
end PrivilegiosDelSistema;
/



create or replace procedure AgregarPrivilegioDelSistema(p_privilegio USER_SYS_PRIVS.PRIVILEGE%TYPE,
                                                        p_opcAdmin USER_SYS_PRIVS.ADMIN_OPTION%TYPE,
                                                        p_nuevoRol varchar2)
is
begin
    if p_opcAdmin='YES' then
        dbms_output.put_line('GRANT '||p_privilegio||' TO '||p_nuevoRol||' WITH ADMIN OPTION;');
    else
        dbms_output.put_line('GRANT '||p_privilegio||' TO '||p_nuevoRol||';');
    end if;
end AgregarPrivilegioDelSistema;
/
~~~



### Alumno 2 (Postgres y ORACLE):

## Postgres:
**1) Averigua que privilegios de sistema hay en Postgres y como se asignan a un usuario.**

Los privilegios de sistema en Postgresql se deteminan a través de los roles, que a su vez son los usuarios y pueden tener asignados otros roles. 

Para realizar una correcta administración de los privilegios del sistema sobre los usuarios se aconseja agrupar los usuarios en diferentes roles siendo la diferencia principal entre los usuarios y los roles de grupo el privilegio de login que tendrían los primeros. 

Estas opciones pueden asignarse a los roles en la propia creación del rol o con posterioridad:
~~~
CREATE ROLE <nombre_rol> 
       WITH <opcion>;
ALTER ROLE <nombre_rol> 
      WITH <opcion>;
~~~

Algunas de estas opciones, y sus contrario, son:
- SUPERUSER/NOSUPERUSER: se agregan los privilegios de superusuario.
- CREATEDB/NOCREATEDB: opción para crear bases de datos.
- CREATEROLE/NOCREATEROLE: opción para crear nuevos roles.
- VALID UNTIL: indica la expiración del rol/usuario. 
- [ENCRYPTED] PASSWORD: asigna una contraseña al rol/usuario.
- LOGIN/NOLOGIN: opción para crear sesiones.
- INHERIT/NOINHERIT: opción para determinar si hereda los privilegios de los roles de los que es miembro.
- REPLICATION/NOREPLICATION: opción para controlar la transmisión.
- BYPASSRL/NOBYPASSRLS: opción para omitir los sistemas de seguridad de fila de las tablas.
- CONNECTION LIMIT: limita el número de sesiones concurrentes. 
- IN ROLE: opción para indicar los roles de los que formará parte.
- ADMIN: opción para indicar el rol o los roles de los formará parte con derecho a agregar a otros roles en este. 


**2) Averigua cual es la forma de asignar y revocar privilegios sobre una tabla concreta en Postgres.**

La sintaxis que se utiliza para otorgar privilegios sobre una tabla en Postgrest es:
~~~
GRANT <nombre_privilegio> 
   ON <nombre_tabla> 
   TO <nombre_rol | nombre_grupo_rol | PUBLIC> 
   [WITH GRANT OPTION]
~~~

Los roles que pueden otorgar estos privilegios son:
- Rol superusuario.
- Rol propietario de la tabla.
- Rol que ha recibido el privilegio con la opción WITH GRANT OPTION.

Para revocar privilegios de una tabla concreta la sintaxis es:
~~~
REVOKE <nombre_privilegio> 
    ON <nombre_tabla> 
    FROM <nombre_rol | nombre_grupo_rol | PUBLIC>
~~~

También se puede eliminar la opción WITH GRANT OPTION, no el privilegio, de un rol concreto con GRANT OPTION FOR.


**3) Averigua si existe el concepto de rol en Postgres y señala las diferencias con los roles de ORACLE.**

Una de las diferencias entre ORACLE y Postgres es que en el segundo, aunque en ocasiones se utiliza el término usuario, solo se trabaja con roles. Mientras que en ORACLE los roles son grupos de usuarios o/y de otros roles, en Postgres los roles son los propietarios de las bases de datos y pueden estar compuestos a su vez de otros roles. 

La creación de roles y la asignación de privilegios sobre objetos a estos comparten la misma sintaxis en ambos gestores:
~~~
CREATE ROLE <nombre_rol>;
GRANT <privilegio> 
    ON <nombre_objeto> 
    TO <nombre_rol>;
~~~

Mientras que la asignación de roles a otros roles es igual, en Postgres no se permite asignar roles a un usuario porque no existe este término.

> Asignación de un rol a un usuario en ORACLE: 
~~~
GRANT <nombre_rol> 
   TO <nombre_usuario>;
~~~

> Asignación de un rol a otro rol en ORACLE y Postgres:
~~~
GRANT <nombre_rol> 
   TO <nombre_rol>;
~~~

Para listar los roles, y los privilegios de estos, en ORACLE se debe consultar, en el diccionario de datos, las vistas DBA_ROLES, DBA_ROLE_PRIVS y ROLE_ROLE_PRIVS. En Postgres se utiliza \du+.



**4) Averigua si existe el concepto de perfil como conjunto de límites sobre el uso de recursos o sobre la contraseña en Postgres y señala las diferencias con los perfiles de ORACLE.**

En Postgres no existe el concepto perfil porque, mientras este término en ORACLE trabaja sobre los usuarios, en Postgres todas las delimitaciones se resuelven sobre los objetos.



**5) Realiza consultas al diccionario de datos de Postgres para averiguar todos los privilegios que tiene un usuario concreto.**

~~~
select 'Privilegio '||privilege_type||
       ' en la tabla '||table_name||
       ' del esquema '||table_schema||
       ' de la base de datos '||table_catalog
from information_schema.table_privileges 
where grantee=<nombre_rol>;
~~~


**6) Realiza consultas al diccionario de datos en Postgres para averiguar qué usuarios pueden consultar una tabla concreta.**
~~~
select grantee
from information_schema.table_privileges
where table_name = <nombre_table>
and privilege_type = 'SELECT';
~~~


## ORACLE:

**7) Realiza una función de verificación de contraseñas que compruebe que la contraseña difiere en más de tres caracteres de la anterior y que la longitud de la misma es diferente de la anterior. Asígnala al perfil CONTRASEÑASEGURA. Comprueba que funciona correctamente. También debe tener mínimo dos letras y mínimo dos números.**

### Creación de la función y los procedimientos correspondientes para la verificación de la password
~~~
create or replace function VerificacionPSW (p_usuario varchar2,
                                            p_pswnueva varchar2,
                                            p_pswvieja varchar2)
return boolean
is
    v_sumaRepe number:=0;
    v_letraigual number:=0;
    v_numNum number:=0;
    v_numLetra number:=0;
    v_validar number:=0;
begin
    if length(p_pswnueva)=length(p_pswvieja) then
        raise_application_error(-20003, 'La nueva contraseña no debe tener la misma longitud que la anterior');
    end if;
    for i in 1..length(p_pswnueva) loop
        ContarNumerosYLetras(substr(p_pswnueva, i,1), v_numNum, v_numLetra);
        CompararCaracteres(substr(p_pswnueva, i,1), p_pswvieja, v_letraigual);
        if v_letraigual=0 then
            v_sumaRepe:=v_sumaRepe+1;
        end if;
        v_letraigual:=0; 
    end loop;
    v_validar:=LanzarErrores(v_sumaRepe, v_numNum, v_numLetra);
    return TRUE;
end VerificacionPSW;
/

create or replace function LanzarErrores(p_sumaRepe number, 
                                         p_numNum number, p_numLetra number)
return number
is
begin
    case
        when p_sumaRepe<4 then
            raise_application_error(-20002, 'La nueva contraseña debe tener al menos 3 caracteres diferentes con respecto a la antigua');
        when p_numNum<2 then
            raise_application_error(-20003, 'La nueva contraseña debe tener al menos 2 letras');
        when p_numLetra<2 then
            raise_application_error(-20004, 'La nueva contraseña debe tener al menos 2 caracteres numericos');
        else
            return 1;
    end case;          
end LanzarErrores;
/


create or replace procedure CompararCaracteres(p_caracter varchar2,
                                               p_psw varchar2,
                                               p_letraigual in out number)
is
begin
    for i in 1..length(p_psw) loop
        if substr(p_psw,i,1)=p_caracter then
            p_letraigual:=1;
        end if;
    end loop;
end CompararCaracteres;
/


create or replace procedure ContarNumerosYLetras (p_caracter varchar2,
                                                  p_numero  in out number,
                                                  p_letra   in out number)
is
begin
    if p_caracter=REGEXP_REPLACE(p_caracter,'[0-9]') then
        p_numero:=p_numero+1;
    else
        p_letra:=p_letra+1;
    end if;
end ContarNumerosYLetras;
/
~~~


# Creación del perfil 
~~~
create profile PswSegura limit 
    PASSWORD_VERIFY_FUNCTION VerificacionPSW;

drop profile PswSegura;
~~~

# Asignación al usuario
~~~
alter user pruebapaloma profile PswSegura;
~~~


# Comrpobación 
~~~
SQL> alter user pruebapaloma identified by asdfghjklñ45 replace asdfghjklñ456;
alter user pruebapaloma identified by asdfghjklñ45 replace asdfghjklñ456
*
ERROR en línea 1:
ORA-28003: fallo en la verificación de la contraseña especificada
ORA-20002: La nueva contraseña debe tener al menos 3 caracteres diferentes con
respecto a la antigua

SQL> alter user pruebapaloma identified by 12345745 replace asdfghjklñ456;
alter user pruebapaloma identified by 12345745 replace asdfghjklñ456
*
ERROR en línea 1:
ORA-28003: fallo en la verificación de la contraseña especificada
ORA-20003: La nueva contraseña debe tener al menos 2 letras

SQL> alter user pruebapaloma identified by zxcvbnm replace asdfghjklñ456;
alter user pruebapaloma identified by zxcvbnm replace asdfghjklñ456
*
ERROR en línea 1:
ORA-28003: fallo en la verificación de la contraseña especificada
ORA-20003: La nueva contraseña debe tener al menos 2 caracteres numericos

SQL> alter user pruebapaloma identified by asdfghjklñ456 replace aaa2222;

Usuario modificado.

SQL> alter user pruebapaloma identified by zxcvbnm123 replace asdfghjklñ456;

Usuario modificado.

SQL> alter user pruebapaloma identified by qwertyu789 replace zxcvbnm123;
alter user pruebapaloma identified by qwertyu789 replace zxcvbnm123
*
ERROR en línea 1:
ORA-28003: fallo en la verificación de la contraseña especificada
ORA-20003: La nueva contraseña no debe tener la misma longitud que la anterior
~~~
    

**8) Realiza un procedimiento llamado MostrarPrivilegiosdelRol que reciba el nombre de un rol y muestre los privilegios de sistema y los privilegios sobre objetos que lo componen.**

~~~
exec MostrarPrivileciosdelRolPaloma ('Dsvret')

create or replace procedure MostrarPrivileciosdelRolPaloma (p_rol varchar2)
is
    v_validacion number:=0;
begin
    v_validacion:=ComprobarSiRolExiste(p_rol);
    if v_validacion=0 then
        BuscarPrivilegiosDelSistema(p_rol);
        dbms_output.put_line(' ');
        dbms_output.put_line('--------------------------------------------------------------------------------');
        dbms_output.put_line(' ');
        BuscarPrivilegiosSobreObjetos(p_rol);
    end if;
end MostrarPrivileciosdelRolPaloma;
/

create or replace procedure BuscarPrivilegiosDelSistema(p_rol varchar2)
is
    cursor c_sys
    is
    select distinct privilege
    from role_sys_privs
    where role in (select distinct role 
                   from role_role_privs 
                   start with role=p_rol
                   connect by role = prior granted_role)
    or role = p_rol;
    v_sys c_sys%ROWTYPE;
begin
    dbms_output.put_line('PRIVILEGIOS DEL SISTEMA');
    dbms_output.put_line('--------------------------------------------------------------------------------');
    for v_sys in c_sys loop
        dbms_output.put_line(v_sys.privilege);
    end loop;
end BuscarPrivilegiosDelSistema;
/


create or replace procedure BuscarPrivilegiosSobreObjetos(p_rol varchar2)
is
    cursor c_tab
    is
    select distinct privilege, table_name, owner
    from role_tab_privs
    where role in (select distinct role 
                   from role_role_privs 
                   start with role=p_rol
                   connect by role = prior granted_role)
    or role = p_rol;
    v_tab c_tab%ROWTYPE;
begin
    dbms_output.put_line('PRIVILEGIOS SOBRE OBJETOS');
    dbms_output.put_line('--------------------------------------------------------------------------------');
    for v_tab in c_tab loop
        dbms_output.put_line(v_tab.privilege||' sobre la tabla '||v_tab.table_name||' del usuario '||v_tab.owner);
    end loop;
end BuscarPrivilegiosSobreObjetos;
/


create or replace function ComprobarSiRolExiste(p_rol varchar2)
return number
is
    v_resultado varchar2(30);
begin
    select role into v_resultado
    from dba_roles
    where role=p_rol;
    return 0;
exception
    when NO_DATA_FOUND then
        dbms_output.put_line('No existe el rol '||p_rol);
        return -1;
end ComprobarSiRolExiste;
/
~~~


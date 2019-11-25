0.# Esquema de usuario en ORACLE
Esquema: conjunto de objetos que es propiedad de un usuario concreto. 

## Creacion, modificacion y borrado de usuarios
create user ___
identified by ____
[default tablespace ___] --> se indica donde guardar las cosas en el tablespace
[tempory tablespace ___] --> los tablespace temporales son determinadas acciones que se guardan en tablespace porque la memoria del equipo a veces no da abasto.
[quota {num{K|M} | unlimited} ON nombre_tablespace]
[profile perfile]; --> conjunto de límites de cuánto puede usar o conjunto de características que van a tener la contraseña (camibar semanalmente, los caracteres, etc). Los perfiles deben de crearse con anterioridad.


Modificar:
alter user ___

borrar:
drop user___[CASCADE];

## Vistas importantes
- DBA_USERS: todos los usuarios
- DBA_TS_QUOTAS: cuotas de tablespace de los usuarios.


## Asignacion y revocación de privilegios
Hay dos tipos de privilegios:
- Privilegios sobre objetos.
	GRANT privilegios ON propietario.objeto TO [usuario | rol | PUBLIC] [with admin option]; --> con esta opción puede darle la opción de repartir, a otro usuarios, el permiso al usuario al que se lo esta otorgando.

- Privilegios del sistema. Permite realizar determinadas operaciones que no están asociadas a objetos, por ejemplo crear tablas, es una accion pero no es de un objeto.
* Create, alter, drop
* create any, alter any, drop any --> para hacer lo que sea con objetos que no son propios sino de otros usuarios. 
* select, insert, update o delete.

GRANT privilegios TO [usuario | rol | PUBLIC] [with admin option];


Quitar privilegios:
REVOKE privilegio FROM [usuario | rol];


Vistas más importantes para un DBA:
- DBA_SYS_PRIVS: todos los privilegios de sistema que se han concedido.
- DBA_TAB_PRIVS: todos los privilegios de objetos que se han concedido.
- DBA_COL_PRIVS: privilegios sobre columnas. 

# Gestion de roles
Son conjunto de privilegios, se pueden crear desde cero, pero por lo general es con roles predefinidos, los más importantes son:
connect, resource y dba.
- connect: el más básico.
- resource: los programadores. 
- DBA: de administración. Este no se suele usar.

Crear un rol: create rol nombre;
Añadir privilegios: grant priv [on obj] to nombrerol;
Para quitar privilegios: revoke priv from nombrerol;
Para dárselo a un usuario: grant nombrerol to usuario;
Para quitárselo a un usuario: revoke nombrerol to from usuario;
Para borrar el rol: drop role nombrerol;
Para añadir un rol a otro: grant nombrerol to nombrerol;

DBA_ROLES: todos los roles
DBA_ROLE_PRIVS: roles concedidos a usuarios
ROLE_ROLE_PRIVS: roles concedidos a otros roles
ROLE_SYS_PRIVS: provolegios de sistema concedidos a los roles
ROLE_TAB_PRIVS: privilegios sobre objetos concedidos a los roles


# Gestión de perfiles
Cada usuario solo puede tener un perfil. Por defecto están desahibilitados. Para habilitarlos:
~~~
alter system set resource_limit=TRUE
~~~

Crear perfil:
CREATE PROFILE nombreperfil LIMIT
{parametrorecurso | parametrocontraseña} [valor[|]]...







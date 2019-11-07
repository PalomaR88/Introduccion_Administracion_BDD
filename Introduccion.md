# Arquitectura en niveles de las bases de datos
Consta de 3 niveles:
- Nivel externo o de visión: los que ven los usuarios de la base de datos. Lo que un usuario ve es una parte de la base de datos, no se ve completa, solo lo que el usuario necesita. Esto se implementa en la realidad a base de permisos. Cuando entramos en el diccionario de datos desde el usuario system, tambien somos usuarios normales y solo vemos vistas de las tablas del diccionario.
- Nivel conceptual: Las vistas que peuden ser los usuarios son las "tablas", son lo que ven los usuarios avanzados o los programadores. Porque los usuarios anteriores realmente no ven las tablas, sino los las vistas/select de determinadas tablas. 
- Niver interno: DBA, la base de datos ya no son tablas, sino ficheros. Normalmente un DBA sabe como están mapeadas las tablas según los discos.

## Ventajas
- Independencia lógica: se puede cambiar tablas, crear columnas, etc. Y lo que funcionaba seguirá funcionando.
- Independencia física: esto permite cambier la ubicación de los ficheros que contienen los datos sin que se vena afectadas las aplicaciones. De esta forma, si, por ejemplo, se llena un disco, se puede añadir otro e ir guardando las nuevas tablas en el nuevo disco y el gestor es el que se va a encargar de localizar esa tabla entre los discos. 

# Componentes de un SGBD
## LENGUAJE
Lo que permite:
- Crear la estructura. DDL
- Consultar y manipular la información almacenada en la base de datos. Ej: DML.
- Asignar privilegios a usuarios, confirmar o abortar transacciones, etc. Ej: DCL.
- En algunos casos, también incluyen formas de manipular aplicaciones.

## EL DICCIONARIO DE DATOS
Contiene los metadatos (datos acerca de los datos) de la base de datos:
- La definición de todos los objeteos existentes en la base de datos: tablas con sus columnas, vistas, procedimientos, triggers, índice, etc...
- La ubicación física de los objetos y el espacio asignado a los mismos..
- Los privilegios y los roles asignados a los usuarios.
- Restricciones de las tablas.
- Etc...

ss644.com/orad/ -> en esta página vienen explicadas las vistas para gestionar la base de datos.

DBA_TABLES-> se ven todas las tablas de todos los usuarios
ALL_TABLES-> se ven todas las tablas que puedes ver, las que son tuyas y a las que puedes acceder.
USER_TABLE-> las tablas de las que eres propietario.

## MECANISMOS DE SEGURIDAD E INTEGRIDAD
- Copias de seguridad.
- Garantizar la proteccion de los datos.
- Restricciones de integridad.
- Recurperar la base de datos hasta un estado consistente en caso de error del sistema. Para esto se neceista un archivo de log, para guardar las acciones que se han realizado y modificado la misma desde la última copia de seguridad. 
- Controlar el acceso concurrente de los usuarios apra evitar errores de integridad. 

## EL FACTOR HUMANO
- Usuarios finales.
- Programadores.
- Administradores o DBAs: garantizan el correcto funcionamiento de la base de datos y  gestionan todos los recursos. Tienen el nivel más alto de provilegios y responsabilidades legales en caso de que los datos tengan alǵun tipo de protección. Su objetivo es que las bases de datos estén siempre disponible y con un rendimiento óptimo. 


# Tareas de un DBA
- Decidir el SGBD idóneo, instalarlo y configurarlo inicialmente.
- Supervisar diseño lógico de la BD.
- Realizar diseño físico de la BD: Estructura de almacenamiento.
- Crear y mantener el esquema de la BD.
- Crear y mantener cuentas de usuarios.
- Colaborar en la formación de usuarios y programadores.
- Detectar y resolver problemas de rendimiento de la BD usando herramientas de monitorización.
- Realizar copias de seguridad, migraciones, importaciones y exportaciones, auditorias de seguridad, etc...
- Recuperar isntancias dañadas.
- Instalar y configurar middleware de la BD.





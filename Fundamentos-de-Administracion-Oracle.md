# Arquitectura interna
## Archivos
- Archivos de datos: las tablas, el diccionario de datos y el segmento de rollback. Las tablas se agrupan en tablespaces y estos a su vez se reparten en los datafiles. Por defecto Oracle trae una serie de tablespaces donde se guardan cierta información (diccionario de datos y usuarios) estos dos tablespaces se recomiendan que estén en discos distintos.
- Archivos de configuración: .ora. Hay parámetros y valor asociado. 
- Archivos de control: .ctl, se consultan mediante vistas dinámicos y solo son modificados por el servidor. Mantienen la integridad de la BS. 
- Archivos de log: se registra los cambios que se han producido. 

## Memoria
- Cache de instrucciones: si la instruccion está cacheada se ahorra compilarla.
- Cache de datos: lo mismo que lo anterior pero con los datos, también hace lo mismo con datos del diccionario de datos.
- Cache redo log: se guarda lo que se va a guardar en el log, pero se cachea la información hasta que tiene bastante y entonces se vuelca en disco. 

## Vistas dinámicas de rendimiento
Para ver el valor actual de un parámetro de inicialización de arranque se puede usar el comando show parameter.

# Instancias
Una instancia es una base de datos que se está ejecutando.

En la instalación se pregunta:
- DLTP: va a tener muchas consultas pero muy sencillas. Se usan en internet.
- Data Mining: bases de datos en las que hay pocas consultas al dia pero muy complejas. Se usan en soportes de tomas de decisiones.
- Proposito general: el término intermedio.

Según la opción que se elija se otorgan determinados parámetros por defecto. 

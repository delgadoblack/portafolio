[SQL injection attack, querying the database type and version on MySQL and Microsoft](https://portswigger.net/web-security/sql-injection/examining-the-database/lab-querying-database-version-oracle)

El enunciado nos dice que podemos realizar las consultas desde el mismo endpoint, pero podemos usar la sentencia `UNION` para concatenar otra sentencia

Investigando en internet, se necesita de una tabla `"dummy"` para ejecutar consultas, que no hacen referencias a una tabla real, para este ejercicio se necesita hacer referencia a una tabla real para poder sacar el valor requerido, por lo que la tabla `"dummy"` existe en el sistema de Oracle y podemos hacer uso de esta tabla para poder realizar peticiones a la tabla de una sola fila para poder ir sacando los datos que necesitamos.

Las pruebas se harían de la siguiente manera:
`' union select 'abc', '...', ... from dual --`  hasta llegar al limite de la tabla y la columna.

Con esta sentencia obtenemos el error: `' union select 'abc' from dual --`
![[Pasted image 20260329200148.png]]

Agregando otro campo obtenemos una respuesta OK: 
`' union select 'abc', 'def' from dual --`
![[Pasted image 20260329200307.png]]

Agregando otro campo, obtenemos de nuevo el error 'Internal Server Error':
`' union select 'abc', 'def', 'qwe' from dual --`
![[Pasted image 20260329200508.png]]

Por lo que podemos deducir que solo necesitamos 2 campos para esta prueba.
Lo que debemos hacer ahora es buscar la manera de realizar una consulta para sacar la versión de oracle, viendo la [documentacion de Oracle](https://docs.oracle.com/en/database/oracle/oracle-database/21/refrn/V-VERSION.html) vemos que la columna BANNER es la encargada del nombre y la versión de oracle, por lo que la consulta quedaría de la siguiente manera:
`' union select BANNER, null from v$version --`
Obteniendo como resultado:
![[Pasted image 20260329201237.png]]

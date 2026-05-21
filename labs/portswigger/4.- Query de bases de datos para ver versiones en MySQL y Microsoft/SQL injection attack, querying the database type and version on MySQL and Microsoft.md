[SQL injection attack, querying the database type and version on MySQL and Microsoft](https://portswigger.net/web-security/sql-injection/examining-the-database/lab-querying-database-version-mysql-microsoft)
MySQL y Microsoft funcionan de la misma manera, por lo que se haría de la siguiente manera:
`' UNION SELECT null,null#`
Pero en este reto, el `#` no lo detecta correctamente, ya que el comentario deberia ser con este tipo, por lo que se debe hacer para colocarlo correctamente seria encodearlo en formato url `%23`
quedando de la siguiente manera: `' union select null,null%23`

![[Pasted image 20260329212129.png]]

teniendo esto listo, investigamos que la manera correcta de sacar la versión de MySQL y Microsoft es `@@version` y no necesitamos apuntar a una tabla real para poder ejecutar esta sentencia:

![[Pasted image 20260329212223.png]]

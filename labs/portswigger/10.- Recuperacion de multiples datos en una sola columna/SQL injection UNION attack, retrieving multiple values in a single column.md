[SQL injection UNION attack, retrieving multiple values in a single column](https://portswigger.net/web-security/sql-injection/union-attacks/lab-retrieve-multiple-values-in-single-column)

Mismos pasos que en el anterior laboratorio pero ahora el que nos devuelve el valor string es solo la segunda columna, por lo que al momento de hacer la peticion, tendremos que meter las 2 columnas en el mismo campo de la tabla para extraerlo de la siguiente manera:
`' union select null,username || '-' || password from users -- -`
![[Pasted image 20260403211626.png]]

E iniciamos sesion con las credenciales de `administrator`
![[Pasted image 20260403211716.png]]

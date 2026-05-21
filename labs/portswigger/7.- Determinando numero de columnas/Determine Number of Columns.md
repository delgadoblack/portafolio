[Lab: Determine Number of Columns](https://portswigger.net/web-security/sql-injection/union-attacks/lab-determine-number-of-columns)

Lo siguiente es validar que podamos determinar el número de columnas disponibles en la ejecución, para esto se utiliza el parámetro `ORDER BY` e ir iterando hasta alcanzar el mensaje de error nuevamente.

Sentencia:
' ORDER BY <número> --

Para resolver el laboratorio, tendremos que ingresar la sentencia:

`` union select null,null,null -- -`
![[Pasted image 20260331174428.png]]

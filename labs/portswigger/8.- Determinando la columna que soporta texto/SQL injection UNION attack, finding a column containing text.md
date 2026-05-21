[SQL injection UNION attack, finding a column containing text](https://portswigger.net/web-security/sql-injection/union-attacks/lab-find-column-containing-text)
Iniciando en el laboratorio nos indica que la BD debe devolver el valor subrayado en rojo
![[Pasted image 20260331175952.png]]

Ya identificamos que son 3 columnas, por lo que deberia ser la misma sentencia e ir iterando con el valor que nos indica el laboratorio hasta encontrar el que soporta el String, que en este caso es la segunda columna:
![[Pasted image 20260331180202.png]]

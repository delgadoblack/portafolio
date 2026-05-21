[SQL injection with conditional responses](https://portswigger.net/web-security/sql-injection/blind/lab-conditional-errors)

En el enunciado tenemos:  
Necesitamos explotar la vulnerabilidad Blind SQL Injection de una tabla llamada `users` con las columnas `username` y `password` para poder sacar la contraseña del usuario `administrator`.

Accedemos al portal e iniciaremos Burpsuite para hacerlo de una manera mas agil ya que necesitaremos interceptar la peticion ya que este metodo viaja mediante la cookie.
![[Pasted image 20260406123145.png]]

Enviamos al modulo de Repeater con CTRL+R
![[Pasted image 20260406123205.png]]

Cuando enviamos una petición y obtenemos una respuesta positiva, el portal nos responde con el mensaje "Welcome Back"
![[Pasted image 20260406123252.png]]

Para validar que sea vulnerable a una inyeccion SQL debemos realizarlo con una condicional, en este caso usaremos la vieja y confiable `' or 1=1 -- -` pero el payload debe viajar en urlEncode, por lo que deberemos seleccionar la cadena de texto (payload) y presionar las teclas CTRL+U
![[Pasted image 20260406123423.png]]

	Realizamos la enumeración de usuario y contraseña, en el enunciado nos dice que podemos realizarlo con las columnas 'username' y 'password' desde la tabla 'users'

Sentencia: `' union select username from users where username='administrator' -- -`

Si esta sentencia nos devuelve una respuesta positiva, nos deberia devolver el mensaje "Welcome Back"
![[Pasted image 20260406123621.png]]

Ahora poder llegar a obtener la contraseña, deberemos conocer la longitud de la misma, para esto utilizaremos el condicional de length.

	Sentencia: `' union select username from users where username='administrator' and length(password)=1 -- -`

Para esto podemos automatizarlo en el modulo intruder, enviaremos la peticion con la combinacion de teclas CTRL+i
![[Pasted image 20260406125122.png]]

En caso de que la peticion este seleccionada automaticamente, limpiaremos todo y seleccionamos solo el numero 1 para el parametro y nos aseguramos que tengamos el tipo de ataque en `Sniper`.
![[Pasted image 20260406125136.png]]

En el modulo de Payloads seleccionaremos 'Numbers' en Type, en el Number range ingresaremos 1 en From y 30 en To con un step de 1 y de manera secuencial.
![[Pasted image 20260406125233.png]]

Y en el modulo de Setting en el Grep-Match limpiaremos todo y agregaremos el mensaje ok "Welcome Back" que sera nuestro filtro de respuesta correcta y le damos en iniciar ataque
![[Pasted image 20260406125345.png]]
![[Pasted image 20260406125602.png]]

Sabiendo que es 20, ahora modificaremos la condicional con subtring de la password en la posicion 1 con la longitud de 1 caracter e iniciamos probando con el caracter "a"

Sentencia: ' union select username from users where username='administrator' and substring(password,1,1)='a' -- -

Copiamos y pegamos la sentencia en el intruder, y los parámetros a modificar serán como primera posición el carácter 'a' y como segunda posición se dejara tal cual, y el tipo de ataque ya no será Sniper, sino `Cluster bomb` ya que tenemos sentenciados 2 parámetros para esta prueba.

ejemplo:
![[Pasted image 20260406165808.png]]

En el primer payload nos posicionaremos en el TO y pondremos 20 ya que es el largo maximo de la password y nos aseguramos de que este en tipo numeros
![[Pasted image 20260406170349.png]]

Y en el segundo payload setearemos el tipo de payload en Brute forcer ya que este nos devolverá una lista de caracteres en minúsculas y números:
![[Pasted image 20260406170510.png]]

y vamos anotando los que va encontrando:
![[Pasted image 20260407004024.png]]

Ingresamos con la password detectada y el usuario administrador para resolver el reto:
![[Pasted image 20260407004121.png]]

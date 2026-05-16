# PortSwigger-Labs
## SQL Injection: Vulnerabilidad en cláusula WHERE
### Descripción
Este laboratorio contiene una vulnerabilidad de inyección SQL en el filtro de categoría de producto. La aplicación realiza una consulta SQL que filtra los productos por categoría y por su estado de publicación (lanzado = 1).

### Objetivo
Realizar un ataque SQLi para recuperar productos no publicados (donde lanzado = 0).

### Metodología
* Identificación: Se observó que el parámetro category en la URL se utiliza directamente en la consulta a la base de datos.

* Explotación: Se utilizó una condición lógica siempre verdadera (OR 1=1) y caracteres de comentario (--) para anular el resto de la consulta original.

### Payload utilizado:
```bash
'+OR+1=1--
```

### Resultado
Al inyectar el payload, la consulta resultante ignora el filtro de seguridad:
SELECT * FROM productos WHERE categoria = 'Gifts' OR 1=1--' AND lanzado = 1
Esto permitió visualizar la totalidad del inventario, resolviendo exitosamente el laboratorio.

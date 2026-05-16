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

# Laboratorio: SQL Injection en Cláusula WHERE (Recuperación de Datos Ocultos)
* **Plataforma**: PortSwigger Academy
* **Dificultad**: Aprendiz
* **Objetivo**: Evadir el filtro de visibilidad de productos para acceder a inventario no publicado.

##  1. Marco de Referencia
| Marco | Id | Categoria / Nombre |
|-------|----|--------------------|
| MITRE ARR&CK | T1190 | Exploit Public-Facing Application |
| CWE | CWE-99 | Improver Neutralization of Special Elements used in an SQL Command |
| NIST SP 800-53 | SI-10 | Información Input Validation |

## 2. Metodología de Pruebas (PTES Modificado)
1. **Análisis de Reconocimiento:** Identificación de parámetros en la URL (`?category=`) que interactúan con la base de datos.
<br>

2. **Análisis de Vulnerabilidades:** Prueba de caracteres especiales (`'`) para provocar errores o cambios en la respuesta del servidor.
<br>

3. **Explotación:** Inyección de lógica booleana para manipular el resultado de la consulta SQL.
<br>

4. **Post-Explotación:** Verificación de acceso a datos que antes estaban ocultos (`released = 0`).

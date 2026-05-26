# SQL injection attack, querying the database type and version on Oracle

## I. Descripción de la vulnerabilidad o ataque
Este laboratorio contiene una vulnerabilidad de inyección SQL en el filtro de categoría de productos. Al ser una base de datos **Oracle**, la consulta e interrogación del sistema para determinar la versión del software requiere una sintaxis específica. A diferencia de otros motores, Oracle exige que toda instrucción `SELECT` apunte a una tabla existente; para consultas genéricas o de variables de entorno, se debe invocar la tabla del sistema integrada llamada `DUAL`. El objetivo del atacante es realizar un ataque de tipo UNION para inyectar una consulta que extraiga la versión de la base de datos.

## II. Tabla de Códigos de Referencia (NIST, MITRE, CWE)

| Marco de Referencia | Código / Identificador | Descripción |
| :--- | :--- | :--- |
| **CWE** | CWE-89 | Improper Neutralization of Special Elements used in an SQL Command ('SQL Injection') |
| **MITRE ATT&CK** | T1190 | Exploit Public-Facing Application (Initial Access) |
| **MITRE ATT&CK** | T1505.003 | Server Software Component: Web Shell / Execution |
| **NIST SP 800-53** | SI-10 | Information Input Validation |
| **OWASP Top 10** | A03:2021-Injection | Categoría principal de vulnerabilidades de inyección |

## III. Detección y Explotación Paso a Paso

### Paso 1: Determinación del número de columnas
Antes de realizar un ataque `UNION`, se debe identificar cuántas columnas devuelve la consulta original utilizando variaciones de `ORDER BY`.
```sql
' ORDER BY 1--
' ORDER BY 2--

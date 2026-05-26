# SQL injection attack, querying the database type and version on MySQL and Microsoft

## I. Descripción de la vulnerabilidad o ataque
El filtro de categoría de la aplicación es vulnerable a inyección SQL. En este escenario, el backend interactúa con un motor de base de datos **MySQL** o **Microsoft SQL Server**. El objetivo es determinar las características del entorno del servidor mediante la inyección de funciones específicas que devuelven la versión del software. A diferencia de Oracle, estos motores permiten omitir la cláusula `FROM` en consultas `UNION` sencillas y utilizan caracteres de comentario distintos (como `#` o `-- ` con un espacio obligatorio al final en MySQL).

## II. Tabla de Códigos de Referencia (NIST, MITRE, CWE)

| Marco de Referencia | Código / Identificador | Descripción |
| :--- | :--- | :--- |
| **CWE** | CWE-89 | Improper Neutralization of Special Elements used in an SQL Command ('SQL Injection') |
| **MITRE ATT&CK** | T1190 | Exploit Public-Facing Application (Initial Access) |
| **NIST SP 800-53** | SI-10 | Information Input Validation |
| **OWASP Top 10** | A03:2021-Injection | Categoría principal de vulnerabilidades de inyección |

## III. Detección y Explotación Paso a Paso

### Paso 1: Identificación del número de columnas
Modificamos el parámetro inyectando cláusulas `ORDER BY` consecutivas hasta forzar un error en la aplicación web para deducir el número de campos de la consulta.
```sql
' ORDER BY 1#
' ORDER BY 2#
```

### Paso 2: Comprobación de compatibilidad de tipos
Inyectamos valores nulos o cadenas de texto para comprobar qué columnas renderizan el texto inyectado en la respuesta HTTP:
```sql
' UNION SELECT 'a', 'b'#
```

### Paso 3: Exfiltración de la versión del sistema (Payload Final)
Utilizamos la función global `@@version` para extraer la versión específica de MSSQL o MySQL.
```sql
' UNION SELECT @@version, NULL#
```

## IV. Mitigación
1. Uso de ORM y Sentencias Preparadas: Utilizar tecnologías de abstracción de datos o parametrización nativa en el código fuente de la aplicación (.NET, Java, etc.).

2. Desactivación de Mensajes de Error Detallados: Configurar el servidor web para que no devuelva códigos de error detallados de la base de datos al cliente, limitando el reconocimiento (reconnaissance) por parte de un atacante.

3. Firewall de Aplicaciones Web (WAF): Desplegar firmas de inspección profunda que bloqueen patrones comunes de ataques basados en funciones del sistema como @@version.

## V. Aviso de Seguridad
[!WARNING]**Aviso de Seguridad**: El contenido de este documento tiene fines exclusivamente educativos y de desarrollo profesional en pruebas de penetración autorizadas. La explotación de vulnerabilidades en entornos e infraestructura sin el consentimiento explícito y por escrito del propietario es ilegal y está penada por las leyes de ciberseguridad internacionales y locales. El autor no se hace responsable del mal uso de esta información.

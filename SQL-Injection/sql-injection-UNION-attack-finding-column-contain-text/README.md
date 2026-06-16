# SQL injection UNION attack, finding a column containing text

## I. Descripción de vulnerabilidad o ataque
En este laboratorio, nos enfocaremos en el Ataque basado en UNION. Este método aprovecha el operador UNION de SQL, cuyo propósito es combinar los resultados de dos o más sentencias SELECT en un solo conjunto de resultados.

Para explotar con éxito esta vulnerabilidad y extraer información confidencial de la base de datos (como tablas de usuarios o contraseñas), un atacante no puede simplemente lanzar una consulta arbitraria; debe cumplir estrictamente con las reglas del motor de base de datos. El ataque se divide en dos fases críticas de reconocimiento:

1. Determinar el número de columnas: La consulta inyectada mediante UNION debe devolver exactamente la misma cantidad de columnas que la consulta original. De lo contrario, el sistema generará un error de sintaxis y bloqueará la ejecución.

2. Identificar columnas compatibles con texto: El operador UNION exige que los tipos de datos en las columnas correspondientes sean compatibles. Dado que la información que se desea exfiltrar generalmente consta de cadenas de caracteres (strings), el objetivo de este ejercicio es probar sistemáticamente cada columna utilizando valores NULL y caracteres de prueba. Esto permite descubrir qué posición dentro del flujo de datos es capaz de procesar, renderizar y mostrar texto en la interfaz web.

## II. Tabla de Códigos de Referencia (NIST, MITRE, CWE, SANS)
| Marco de Referencia | Codigo/Identificador | Descripción |
|---------------------|----------------------|-------------|
| CWE | CWE-89 | Improper Neutralization of Special Elements used in an SQL Command ('SQL Injection') |
| MITRE ATT&CK | T1190 | Exploit Public-Facing Application (Initial Access) |
| NIST SP 800-53 | SI-10 | Information Input Validation |
| | SI-11 | Error Handling |
| | SA-11 | Developer Testing and Evaluation |
| | AC-4 | Information Flow Enforcement |
| OWASP Top 10 | A03:2021-Injection | Categoria principal de ataques de Inyección |
| SANS IR | Identificacion | Fase del SANS Incident Handlers Handbook orientada al análisis de telemetría de red y logs web para detectar anomalías provocadas por el testeo iterativo de columnas orientadas a la exfiltración. |


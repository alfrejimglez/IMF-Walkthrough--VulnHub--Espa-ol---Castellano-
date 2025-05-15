# IMF - Walkthrough VulnHub Machine

## Descripción
IMF es una máquina vulnerable de VulnHub que presenta varias vulnerabilidades interesantes, incluyendo inyecciones SQL, bypass de autenticación, vulnerabilidades en la subida de archivos, buffer overflow y escalación de privilegios mediante técnicas avanzadas.  

Este walkthrough detalla cómo se explotaron dichas vulnerabilidades para conseguir todas las flags y acceso root.

## Flags y Vulnerabilidades

### FLAG 1
- Se detectó una librería obsoleta con posible vulnerabilidad.
- Una pista fue una cadena que terminaba con `=`, típica de Base64.
  
### FLAG 2
- Se detectó una ruta oculta `/imfadministrator`.
- La validación del login era débil: el backend en PHP validaba el password sin importar su valor real si se enviaba como un array, lo que permitió bypass.
- Fragmentos en Base64 indicaban un usuario administrador ofuscado: `imfinistrator`.

### FLAG 3
- Se encontró que el CMS usaba inyecciones SQL en el parámetro `pagename`.
- Se usó `sqlmap` para automatizar la enumeración de tablas y datos.
- Se descubrió que la imagen del panel podía verse sin autenticación si se tenía la ruta absoluta.

### FLAG 4
- Subida de archivos vulnerable mediante `uploadr942.php`.
- Se renombró un archivo PHP como GIF agregando la cabecera `GIF8;` para evadir el WAF.
- La webshell subida permitió ejecución de comandos remotos.

### FLAG 5
- Análisis de un binario con GHIDRA reveló vulnerabilidad de buffer overflow.
- La función `gets()` sin límite permitía desbordar el buffer.
- Se usó un patrón único para detectar el offset exacto con GDB.
- Se generó un payload en Python para shell reversa.
- Se detectó que la máquina usaba port knocking para abrir el puerto 7788, necesario para el exploit.

### FLAG 6
- Obtención de acceso root mediante escalación de privilegios

## Herramientas utilizadas
- Burp Suite (para manipulación de peticiones POST)
- sqlmap (automatización de inyección SQL)
- GHIDRA (análisis estático del binario)
- GDB (depurador para análisis del crash y offsets)
- msfvenom (generación de shellcodes)
- knock (port knocking tool): https://github.com/guelfoweb/knock
- Python 3 (para el exploit y payload)

## Paso a paso resumen

1. **Reconocimiento inicial:** Se detectaron librerías obsoletas y pistas en Base64.
2. **Bypass de autenticación:** En `/imfadministrator`, se aprovechó la validación incorrecta del password.
3. **Inyección SQL:** Se usó sqlmap para extraer datos del CMS vulnerable.
4. **Subida de webshell:** Se subió un archivo PHP disfrazado de GIF para evadir el WAF.
5. **Análisis de binario y Buffer Overflow:** Se encontró y explotó un buffer overflow usando GHIDRA, GDB y payloads personalizados.
6. **Port Knocking:** Se abrió el puerto cerrado enviando secuencia de puertos correctos con `knock`.
7. **Escalación a root:** Se exploraron permisos SUID, SGID y sudo para conseguir acceso root pero se comprometio ejecutando el script python generado.

## Referencias y recursos

- Walkthrough original: [GitHub - IMF Walkthrough (Español)](https://github.com/alfrejimglez/IMF-Walkthrough--VulnHub--Espa-ol---Castellano-/blob/main/alfredo_exploit.py)
- Knock tool: [https://github.com/guelfoweb/knock](https://github.com/guelfoweb/knock)
Maquina Subida a mediafire (sin realizar)
[IMF.tar - Descarga aquí](https://www.mediafire.com/file/uuq8p77tshffbl7/IMF.tar/file)


# Ataque DoS con SYN Flood y mitigación mediante firewall

## Introducción

En este laboratorio de ciberseguridad se llevó a cabo una simulación de ataque de denegación de servicio (DoS) tipo **SYN Flood** contra una máquina vulnerable (**Metasploitable 2**) desde una máquina atacante (**Kali Linux**), en un entorno controlado con máquinas virtuales bajo **VMware**.

El objetivo de esta práctica fue **analizar el comportamiento del protocolo TCP ante un ataque de saturación de conexiones** y probar una medida de mitigación utilizando reglas de firewall (`iptables`), observando los resultados mediante **Wireshark**.

---

## Metodología

1. **Inicio del ataque SYN Flood**  
   Se utilizó la herramienta `hping3` desde Kali Linux para lanzar un ataque SYN Flood al puerto 80 de la máquina víctima.

sudo hping3 -c 10000 -d 120 -S -w 64 -p 80 --flood --rand-source 192.168.209.137

2. **Captura de tráfico con Wireshark**  
Se analizó el tráfico en Metasploitable utilizando Wireshark, verificando que llegaban múltiples paquetes TCP con la bandera SYN activada.

3. **Finalización del ataque (manual)**  
El ataque se detuvo desde Kali presionando `Ctrl+C`, lo cual provocó el cese de los paquetes SYN.

4. **Segundo ataque y aplicación de medidas defensivas**  
Se lanzó nuevamente el ataque desde Kali, pero esta vez se ejecutó una regla de firewall en Metasploitable para bloquear las solicitudes provenientes de la IP atacante:

sudo iptables -A INPUT -s 192.168.209.129 -j DROP

5. **Verificación del bloqueo**  
Se verificó en Wireshark que, a pesar de que Kali seguía enviando paquetes, ya no llegaban a la víctima, lo que confirmó que la regla de iptables funcionaba correctamente.

---

## Hallazgos

- **Tipo de ataque:** Denegación de servicio (SYN Flood)
- **Herramienta usada:** `hping3`
- **IP atacante:** 192.168.209.129 (Kali Linux)
- **IP víctima:** 192.168.209.136 (Metasploitable 2)
- **Puerto objetivo:** 80/tcp
- **Medida defensiva:** Bloqueo de IP mediante `iptables`
- **Impacto simulado:** Saturación de la tabla de conexiones, imposibilitando nuevas conexiones legítimas

---

## Pruebas y Evidencias

### Inicio del ataque SYN Flood

- **Comando usado:**
sudo hping3 -S -p 80 --flood 192.168.209.136

- **Descripción:**  
Desde Kali Linux se comenzaron a enviar paquetes TCP con la bandera SYN en modo flood hacia el puerto 80 de la máquina víctima.

- **Captura:**  
*(Imagen 1: Consola de Kali ejecutando el comando de ataque)*

---

### Tráfico observado en Wireshark

- **Descripción:**  
Wireshark mostró una gran cantidad de paquetes SYN entrantes, todos desde la IP atacante.

- **Captura:**  
*(Imagen 2: Wireshark mostrando múltiples solicitudes SYN al puerto 80)*

---

### Fin del ataque

- **Comando usado:** `Ctrl+C` en la terminal de Kali
- **Descripción:**  
El ataque fue interrumpido manualmente. En Wireshark se dejó de observar el tráfico SYN malicioso.

- **Capturas:**  
*(Imagen 3: Wireshark con fin del tráfico)*  
*(Imagen 3.5: Terminal con Ctrl+C ejecutado)*

---

### Mitigación con iptables

- **Comando usado:**
sudo iptables -A INPUT -s 192.168.209.129 -j DROP

- **Descripción:**  
Se bloqueó la IP de Kali desde Metasploitable para evitar que llegaran nuevas solicitudes de conexión.

- **Comentario:**  
La notebook se crasheó durante el segundo ataque simulado así que se redujeron las solicitudes desde Kali.

sudo hping3 -c 5000 -d 60 -S -w 64 -p 80 --flood 192.168.209.136

- **Captura:**  
*(Imagen 4: Consola con iptables ejecutado)*

---

### Verificación de mitigación

- **Descripción:**  
Aunque Kali siguió enviando SYNs, Wireshark ya no mostraba tráfico entrante desde esa IP. La mitigación fue efectiva.

- **Captura:**  
*(Imagen 5: Wireshark sin más paquetes SYN)*

---

## Conclusión

Durante este laboratorio se simuló exitosamente un ataque de denegación de servicio tipo **SYN Flood** y se comprobó su impacto observando el tráfico con **Wireshark**. Luego se implementó una medida de mitigación básica utilizando `iptables` en la máquina víctima, la cual bloqueó el flujo de paquetes provenientes de la IP atacante.

Este ejercicio permitió practicar habilidades clave como análisis de red, detección de ataques mediante sniffers y aplicación de defensas en tiempo real, aportando experiencia práctica para escenarios de seguridad reales.

---

**GitHub:** [https://github.com/Lucased12]
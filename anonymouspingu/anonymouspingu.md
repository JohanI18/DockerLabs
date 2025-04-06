1. Comprobación de Conectividad

ping 172.17.0.2

2. Escaneo de Puertos Abiertos

nmap -p- --open -sS --min-rate 5000 -vvv -n -Pn 172.17.0.2

✅ Puertos abiertos detectados:

    21 (FTP)
    22 (SSH)

3. Detección de Versiones de Servicios

nmap -p21,22,80 -sCV 172.17.0.2

Resultados:
Puerto 	Servicio 	Versión
21/tcp 	FTP 	vsftpd 3.0.5
22/tcp 	SSH 	Apache httpd 2.4.58 (Ubuntu)

4. Acceso al Servicio FTP (Modo Anónimo)

ftp 172.17.0.2

📥 Acceso concedido (anónimo): 230 Login successful



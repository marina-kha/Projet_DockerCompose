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
On utilise les commandes suivantes :
```python 
sudo curl -L https://github.com/docker/compose/releases/download/v2.0.1/docker-compose-linux-x86_64 -o /usr/local/bin/docker-compose
```

```python 
sudo chmod +x /usr/local/bin/docker-compose
```


```python 

```


```python 

```


```python 

```

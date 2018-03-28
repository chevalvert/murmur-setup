# Murmur - guide d'installation

## 1 - Installation de l’image disque sur Raspberry
Installer le zip de l'image Raspberry de Murmur sur la carte microSD .  
Utiliser une carte Kingston microSDHC Class 10 16GB et l'application [Etcher](https://etcher.io/) flasher l'image sur la carte SD.  
[Liste des cartes SD compatibles avec RPi](https://elinux.org/RPi_SD_cards)  

## 2 - Création et installation des clés SSH

### 1 - Générer une clé SSH sur l'ordinateur
- Vérifier s'il existent déjà des clés SSH avec la commande `ls ~/.ssh`  
S'il y a des fichiers nommés `d_rsa.pub` ou `id_dsa.pub` la clé existe déjà. Vous pouvez sauter le pas "Générer une nouvelle clé SSH" (ou supprimer ces fichiers avec la commande `rm id*` et générer une nouvelle clé).

- Générer une nouvelle clé SSH
	- Écrire la commande `ssh-keygen -t rsa -C <YOURNAME>@<YOURDEVICE>` par exemple `ssh-keygen -t rsa -C murmur@pi` 
	- N'écrivez pas de password, appuyez `Enter`

- Vérifier que la clé a été créée avec la commande `ls ~/.ssh`  	
S'il y a des fichiers nommés `d_rsa.pub` ou `id_dsa.pub` la clé a bien été créée.

### 2 - Copier la clé publique SSH sur la Raspberry Pi
- Tout d'abord, connecter par ethernet la Raspberry et l'ordinateur sur le router
- Utiliser l'application [IP Scanner](https://itunes.apple.com/us/app/ip-scanner/id404167149?mt=12) et noter l'adresse IP de la Raspberry
- Pour copier la clé sur la Raspberry utiliser la commande `cat ~/.ssh/id_rsa.pub | ssh <YOURDEVICE>@<IP-ADDRESS> 'cat >> .ssh/authorized_keys'`  
par exemple `cat ~/.ssh/id_rsa.pub | ssh pi@192.168.1.126 'cat >> .ssh/authorized_keys'`
- autentifier le login avec le password `raspberry`

**Note**
Si, lors de la connexion sur la Raspberry le Terminal vous renvoie un message `WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!` suivez ce pas à pas :
- Ouvrez le fichier `/Users/chevalvert/.ssh/known_hosts`
- Effacez le contenu du fichier texte
- Copiez à nouveau la clé publique SSH sur la Raspberry

### 3 - Connexion à la Raspberry Pi en utilisant la clé SSH
Utiliser la commande `ssh pi@<IP-ADDRESS>` et `Enter` pour se connecter à la Raspberry.  
Puique la clé SSH a été installée, la connexion devrait pouvoir se faire sans demande de password.

## 3 - Configuration des adresses IP
Pour rendre possible la communication entre l'ordinateur et la Raspberry il faut configurer et renseigner les adresses IP dans les fichiers de configuration du programme Murmur (dans l'ordinateur et dans la Raspberry). Pour cela il faut connecter l'ordinateur et la Raspberry au router avec 2 câbles ethernet.

### 1 - Sur l'ordinateur
- Éteindre le wifi
- Connecter l'ordinateur au routeur par ethernet 
- Dans `System preferences > Network` vérifier que la connexion ethernet est en mode DHCP
- Ouvrir l'application IP Scanner et noter les adresses IP de l'ordinateur (local user 192.168.1.33) et de la Raspberry (192.168.1.2)
- Ouvrir le fichier `yourmurmurappfolder/data/configuration.xml` avec TextEdit, signaler l'adresse IP de la Raspberry, sauvegarder et fermer 
>	```java
>	<!-- Raspberry -->
>    <launchDevices pathScriptRun="/home/pi/openFrameworks/apps/myApps/murmurRaspberry/bin/run_murmur.sh">   	
>        <ip>192.168.1.2</ip>		
>   </launchDevices>
>   ```

### 2 - Sur la Raspberry
- Se connecter à la Raspberry en utilisant l'application Cyberduck de l'ordinateur
	- SFTP (SSH File Transfer Protocol)
	- Server : addresse IP de la Raspberry
	- username : pi
	- password : raspberry
- Ouvrir le fichier `pi/openFrameworks/apps/myApps/murmurRaspberry/bin/data/configuration.xml` avec TextEdit et signaler l'adresse IP de l'ordinateur (local user), sauvegarder et fermer  
- Pour que l'autorun puisse avoir lieu rendre le fichier  `pi/openFrameworks/apps/myApps/murmurRaspberry/bin/run_murmur.sh` exécutable en faisant click droit > info > permissions execute
- Pour faire la balance du microphone utiliser le terminal pour se connecter à la Raspberry `ssh pi@<IP-ADDRESS>` puis `alsamixer` 

À présent l'ordinateur et la Raspberry connaissent leurs adresses IP et peuvent communiquer.

## 4 - Lancer Murmur en mode installation
- Ouvrir le fichier `yourmurmurappfolder/data/configuration.xml` avec TextEdit et désactiver le mode simulation en écrivant 0 dans `<enable>0</enable>`
- Lancer le programme murmur `yourmurmurappfolder/murmur`. Le stripLED et les projections doivent réagir à la voix (dans le simulateur et dans l'installation). Sinon vérifier que dans l'onglet Configuration la fonction `Launch murmur@rasp` est bien cochée.

## License
[MIT.](https://tldrlegal.com/license/mit-license)
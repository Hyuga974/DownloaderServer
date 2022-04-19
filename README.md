# DownloaderServer

## Sommaire
- [Downloader server](#downloaderserver)
  - [**Sommaire**](#sommaire)
  - [**I. Installation**](#i-Installation)
    - [A. Plex](#a-plex)
    - [B. Radarr](#b-radarr)
    - [C. Sonarr](#c-sonarr)
    - [D. Jacket](#d-jacket)

## I. Installation 

Pour commencer la mise en place de notre service, il faut d'abord tout installer. Quand on dit tout installer on parle de:

- Plex : pour jouer le rôle de lecteur et de repertoire
- Radarr : pour installer des films
- Sonarr : pour installer des séries
- Jacket : pour indéxer

<br>

### A. Plex
<br>
Plex est service qui nous permet d'organiser les films, séries et musiques téléchargés de manière propre. De plus Plex nous sert également de  lecteur multimédia.

Afin de l'installer, il faut suivre les commandes suivantes: 

-> ```sudo apt update && sudo apt upgrade -y```

-> ```sudo apt install apt-transport-https curl wget -y```

```
tamashi-infra@InfraServer:~$ sudo apt install apt-transport-https curl wget -y
Lecture des listes de paquets... Fait
Construction de l'arbre des dépendances... Fait
Lecture des informations d'état... Fait
wget est déjà la version la plus récente (1.21-1+deb11u1).
Les NOUVEAUX paquets suivants seront installés :
  apt-transport-https curl
0 mis à jour, 2 nouvellement installés, 0 à enlever et 0 non mis à jour.
Il est nécessaire de prendre 427 ko dans les archives.
Après cette opération, 602 ko d'espace disque supplémentaires seront utilisés.
Réception de :1 http://deb.debian.org/debian bullseye/main amd64 apt-transport-https all 2.2.4 [160 kB]
Réception de :2 http://deb.debian.org/debian bullseye/main amd64 curl amd64 7.74.0-1.3+deb11u1 [267 kB]
427 ko réceptionnés en 0s (1 373 ko/s)
Sélection du paquet apt-transport-https précédemment désélectionné.
(Lecture de la base de données... 139872 fichiers et répertoires déjà installés.)
Préparation du dépaquetage de .../apt-transport-https_2.2.4_all.deb ...
Dépaquetage de apt-transport-https (2.2.4) ...
Sélection du paquet curl précédemment désélectionné.
Préparation du dépaquetage de .../curl_7.74.0-1.3+deb11u1_amd64.deb ...
Dépaquetage de curl (7.74.0-1.3+deb11u1) ...
Paramétrage de apt-transport-https (2.2.4) ...
Paramétrage de curl (7.74.0-1.3+deb11u1) ...
Traitement des actions différées (« triggers ») pour man-db (2.9.4-2) ...
```

-> sudo wget -O- https://downloads.plex.tv/plex-keys/PlexSign.key | gpg --dearmor | sudo tee /usr/share/keyrings/plex.gpg

```
tamashi-infra@InfraServer:~$ sudo wget -O- https://downloads.plex.tv/plex-keys/PlexSign.key | gpg --dearmor | sudo tee /usr/share/keyrings/plex.gpg
--2022-04-19 10:56:09--  https://downloads.plex.tv/plex-keys/PlexSign.key
Résolution de downloads.plex.tv (downloads.plex.tv)… 104.18.34.20, 172.64.153.236, 2606:4700:4400::ac40:99ec, ...
Connexion à downloads.plex.tv (downloads.plex.tv)|104.18.34.20|:443… connecté.
requête HTTP transmise, en attente de la réponse… 200 OK
Taille : 3072 (3,0K) [binary/octet-stream]
Sauvegarde en : « STDOUT »

-                             100%[=================================================>]   3,00K  --.-KB/s    ds 0s

2022-04-19 10:56:10 (40,2 MB/s) — envoi vers sortie standard [3072/3072]

[...]

d&��( ��S�W��L}����K�����NĘ�t|(D�����=���O��H"���~��7���ez�ŵO;�ʘe� �S�����F�Z;ZԼ�@�B�e�̔�U����3��J��(l�x��9��i�VM�Y��v�}C��a�Z��hB`Ď�A?-��p��hl�@��@CG���;��ީ�?Hi���8
```
-> ```echo deb [signed-by=/usr/share/keyrings/plex.gpg] https://downloads.plex.tv/repo/deb public main | sudo tee /etc/apt/sources.list.d/plexmediaserver.list```

```
tamashi-infra@InfraServer:~$ echo deb [signed-by=/usr/share/keyrings/plex.gpg] https://downloads.plex.tv/repo/deb public main | sudo tee /etc/apt/sources.list.d/plexmediaserver.list
deb [signed-by=/usr/share/keyrings/plex.gpg] https://downloads.plex.tv/repo/deb public main
```

-> ```sudo apt update```

```
tamashi-infra@InfraServer:~$ sudo apt update
Atteint :1 http://security.debian.org/debian-security bullseye-security InRelease
Atteint :2 http://deb.debian.org/debian bullseye InRelease
Atteint :3 http://deb.debian.org/debian bullseye-updates InRelease
Réception de :4 https://downloads.plex.tv/repo/deb public InRelease [6 685 B]
Réception de :5 https://downloads.plex.tv/repo/deb public/main amd64 Packages [464 B]
7 149 o réceptionnés en 1s (12,2 ko/s)
Lecture des listes de paquets... Fait
Construction de l'arbre des dépendances... Fait
Lecture des informations d'état... Fait
Tous les paquets sont à jour.
```

-> ```sudo apt install plexmediaserver```
    Faire Oui à la première question et Non à la seconde.

-> ```systemctl status plexmediaserver```

```
tamashi-infra@InfraServer:~$ systemctl status plexmediaserver
● plexmediaserver.service - Plex Media Server
     Loaded: loaded (/lib/systemd/system/plexmediaserver.service; enabled; vendor preset: enabled)
     Active: active (running) since Tue 2022-04-19 11:00:39 CEST; 3min 6s ago
    Process: 3949 ExecStartPre=/bin/sh -c /usr/bin/test -d "${PLEX_MEDIA_SERVER_APPLICATION_SUPPORT_DIR}" || /bin/mkdir -p "${PLEX_MEDIA_SERVER_APPLICATION_SUPPORT_DIR}" (code=exited, status=0/SUCCESS)
   Main PID: 3954 (Plex Media Serv)
      Tasks: 110 (limit: 9267)
     Memory: 221.8M
        CPU: 20.775s
     CGroup: /system.slice/plexmediaserver.service
             ├─3954 /usr/lib/plexmediaserver/Plex Media Server
             ├─4022 Plex Plug-in [com.plexapp.system] /usr/lib/plexmediaserver/Resources/Plug-ins-965587f64/Framework.bundle/Contents/Resources/Versions/2/Python/bootstrap.py --server-version 1.25.9.5721-9655>
             ├─4079 /usr/lib/plexmediaserver/Plex Tuner Service /usr/lib/plexmediaserver/Resources/Tuner/Private /usr/lib/plexmediaserver/Resources/Tuner/Shared 1.25.9.5721-965587f64 32600
             ├─4099 Plex Plug-in [com.plexapp.agents.thetvdb] /usr/lib/plexmediaserver/Resources/Plug-ins-965587f64/Framework.bundle/Contents/Resources/Versions/2/Python/bootstrap.py --server-version 1.25.9.5>
             ├─4254 Plex Plug-in [org.musicbrainz.agents.music] /usr/lib/plexmediaserver/Resources/Plug-ins-965587f64/Framework.bundle/Contents/Resources/Versions/2/Python/bootstrap.py --server-version 1.25.9>
             ├─4261 Plex Plug-in [tv.plex.agents.music] /usr/lib/plexmediaserver/Resources/Plug-ins-965587f64/Framework.bundle/Contents/Resources/Versions/2/Python/bootstrap.py --server-version 1.25.9.5721-96>
             ├─4267 Plex Plug-in [tv.plex.agents.movie] /usr/lib/plexmediaserver/Resources/Plug-ins-965587f64/Framework.bundle/Contents/Resources/Versions/2/Python/bootstrap.py --server-version 1.25.9.5721-96>
             ├─4427 Plex Plug-in [tv.plex.agents.series] /usr/lib/plexmediaserver/Resources/Plug-ins-965587f64/Framework.bundle/Contents/Resources/Versions/2/Python/bootstrap.py --server-version 1.25.9.5721-9>
             └─4497 Plex Plug-in [com.plexapp.agents.imdb] /usr/lib/plexmediaserver/Resources/Plug-ins-965587f64/Framework.bundle/Contents/Resources/Versions/2/Python/bootstrap.py --server-version 1.25.9.5721>
lines 1-18/18 (END)
```

Et voilà, notre logiciel Plex est installé.

<br>

### B. Radarr

<br>

Radarr est un service qu'il nous permet de rechercher et d'installer des films. 
Pour l'installer:

-> ```sudo apt install curl sqlite3```

```
tamashi-infra@InfraServer:~$ sudo apt install curl sqlite3
[sudo] Mot de passe de tamashi-infra :
Lecture des listes de paquets... Fait
Construction de l'arbre des dépendances... Fait
Lecture des informations d'état... Fait
curl est déjà la version la plus récente (7.74.0-1.3+deb11u1).
Paquets suggérés :
  sqlite3-doc
Les NOUVEAUX paquets suivants seront installés :
  sqlite3
0 mis à jour, 1 nouvellement installés, 0 à enlever et 0 non mis à jour.
Il est nécessaire de prendre 1 201 ko dans les archives.
Après cette opération, 3 155 ko d'espace disque supplémentaires seront utilisés.
Souhaitez-vous continuer ? [O/n] O
Réception de :1 http://deb.debian.org/debian bullseye/main amd64 sqlite3 amd64 3.34.1-3 [1 201 kB]
1 201 ko réceptionnés en 1s (1 893 ko/s)
Sélection du paquet sqlite3 précédemment désélectionné.
(Lecture de la base de données... 142454 fichiers et répertoires déjà installés.)
Préparation du dépaquetage de .../sqlite3_3.34.1-3_amd64.deb ...
Dépaquetage de sqlite3 (3.34.1-3) ...
Paramétrage de sqlite3 (3.34.1-3) ...
Traitement des actions différées (« triggers ») pour man-db (2.9.4-2) ...
```

-> Exécuter le script [ArrInstall](./ArrInstall.sh)

```
tamashi-infra@InfraServer:~$ sudo bash ArrInstall.sh
Running \*Arr Install Script - Version [3.0.5] as of [2022-04-03]
Select the application to install:
1) lidarr
2) prowlarr
3) radarr
4) readarr
5) whisparr
6) quit
#? 3
It is critical that the user and group you select to run Radarr as will have READ and WRITE access to your Media Library and Download Client Completed Folders
What user should Radarr run as? (Default: radarr):
What group should Radarr run as? (Default: media):
Radarr selected
This will install [Radarr] to [/opt/Radarr] and use [/var/lib/radarr/] for the AppData Directory
Radarr will run as the user [radarr] and group [media]. By continuing, you've confirmed that that user and group will have READ and WRITE access to your Media Library and Download Client Completed Download directories
Continue with the installation [Yes/No]?
1) Yes
2) No
#? 1
Ajout de l'utilisateur système « radarr » (UID 118) ...
Ajout du nouvel utilisateur « radarr » (UID 118) avec pour groupe d'appartenance « media » ...
Le répertoire personnel « /home/radarr » n'a pas été créé.
Created and added User [radarr] to Group [media]
User [radarr] did not exist in Group [media]
Added User [radarr] to Group [media]
Directories created
[...]


Install complete
Browse to http://10.0.2.15:7878 for the Radarr GUI
```


### C. Sonarr

### D. Jacket

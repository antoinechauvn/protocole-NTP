# protocole-NTP
Découverte du protocole NTP

### Qu'est-ce que le protocole NTP?
```
Network Time Protocol (« protocole de temps réseau ») a pour objectif de synchroniser l'horloge
locale de votre machine (date et heure) au travers le réseau à l'aide d'une machine de référence,
que l'on appellera le serveur NTP.
```

## Principe de fonctionnement

Le NTP utilise le protocole de transport UDP et le port 123.<br>
Le Network Time Protocol repose sur une architecture hiérarchique en réseau, répartie en plusieurs niveaux (ou strates).<br>
L’heure et la date sont donc diffusées de manière verticale à travers les niveaux, du niveau 1 jusqu’au niveau 15. Dans la majorité des cas, cela ne dépasse pas le 5ᵉ niveau.

![image](https://user-images.githubusercontent.com/83721477/167577346-af47c81a-d2d0-491b-b218-e48ee0ff7a32.png)

0. Au sommet de cette structure, vous trouverez les horloges de référence de temps. Pilotées par radio ou par satellites, ces dernières sont des dérivés des horloges atomiques.
1. En dessous, les serveurs NTP de premier niveau se synchronisent sur les horloges de référence (horloges atomiques).<br>À ce jour, `230 serveurs de niveau 1` sont recensés à travers la planète.
2. Les serveurs NTP secondaires vont alors obtenir l’heure et la date des serveurs du précédent niveau en mode client/serveur.<br>D’autre part, ces mêmes serveurs vont échanger, en mode symétrique, avec d’autres serveurs du même niveau pour s’assurer du bon horodatage (même horaire de référence).
3. Les serveurs de niveau inférieur, ceux du 3ème niveau dans ce cas, utilisent la même méthode de communication à savoir :
  * Un mode client / serveur avec les serveurs NTP de rang supérieur,
  * Et un mode symétrique avec les serveurs NTP de même rang.
* Puis, les clients terminaux (serveurs et ordinateurs) vont alors se synchroniser en mode client/serveur ou en multicast en vue de recevoir un horodatage exact. 

*Note: Toutefois, la présence de clients n’est autorisée qu’à partir du troisième niveau.*

## Modes NTP
#### Mode symétrique actif
Un serveur fonctionnant dans ce mode envoie périodiquement des messages, sans se soucier de l’état de ses voisins (joignables, serveurs primaires ou secondaires, clients). Il indique ainsi sa « volonté » de synchroniser d’autres serveurs et d’être synchroniser par eux.

#### Mode symétrique passif
Ce type d’association est généralement créée lors de l’arrivée sur le serveur d’un message d’un autre serveur (en mode symétrique actif). Le serveur annonce son intention de synchroniser et d’être synchronisé. Seulement il ne le fait qu’après avoir été sollicité par un autre serveur.

#### Mode client
La machine envoie des messages régulièrement, sans se préoccuper de l’état de ses voisins. Elle indique ainsi sa « volonté » d’être synchronisée.

#### Mode serveur
L’association de ce mode est créée lors de la réception d’une requête (un message) d’une station en mode client.

#### Mode broadcast
Destiné aux réseaux locaux, il se limite à une diffusion d’informations horaires pour des clients pouvant être soit passifs, soit découvrant ainsi les serveurs avec lesquels ils vont se synchroniser.

## Avantages et utilisations de NTP
* Donner la meilleure précision possible compte tenu des conditions matérielles
* Résister à une multitude de pannes (y compris les bugs d’implémentation)
* Réaliser l’authentification cryptographique basée sur des infrastructures de clefs symétriques et de clefs publiques

## Configuration Cisco NTP
Par défaut, NTP est désactivé sur le matériel Cisco.
```
Router#show ntp status
%NTP is not enabled.
```

#### Définir l'horloge locale
```
Router#clock set [hh:mm:ss] [month] [day] [year]
```

#### Définir un serveur NTP
```
Router#configure terminal
Enter configuration commands, one per line. End with CNTL/Z.
Router(config)#ntp server 192.168.1.10
Router(config)#exit
```

Tant que la synchronisation n’est pas réalisée, le client est vu en strate 16.
```
Router#sh ntp status
Clock is unsynchronized, stratum 16, no reference clock
nominal freq is 1000.0003 Hz, actual freq is 1000.0003 Hz, precision is 2**17
ntp uptime is 5400 (1/100 of seconds), resolution is 1000
reference time is 00000000.00000000 (00:00:00.000 UTC Mon Jan 1 1900)
clock offset is 0.0000 msec, root delay is 0.00 msec
root dispersion is 0.80 msec, peer dispersion is 0.00 msec
loopfilter state is 'NSET' (Never set), drift is 0.000000000 s/s
system poll interval is 8, never updated.
```

Après un certain temps, et si le client est correctement configuré, on apprend que l’horloge est synchronisée et que le périphérique est situé dans la strate 3.<br>
On y apprend différentes informations sur l’horloge de référence et ses délais.


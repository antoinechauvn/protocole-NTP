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

![image](https://user-images.githubusercontent.com/83721477/167574127-55cc88b5-5358-41cf-a691-e037f2adb257.png)

* Au sommet de cette structure, vous trouverez les horloges de référence de temps. Pilotées par radio ou par satellites, ces dernières sont des dérivés des horloges atomiques.
* En dessous, les serveurs NTP de premier niveau se synchronisent sur les horloges de référence (horloges atomiques).<br>À ce jour, `230 serveurs de niveau 1` sont recensés à travers la planète.
* Par la suite, les serveurs NTP secondaires vont alors obtenir l’heure et la date des serveurs du précédent niveau en mode client/serveur. D’autre part, ces mêmes serveurs vont échanger, en mode symétrique, avec d’autres serveurs du même niveau pour s’assurer du bon horodatage (même horaire de référence).
* Les serveurs de niveau inférieur, ceux du 3ème niveau dans ce cas, utilisent la même méthode de communication à savoir :
  * Un mode client / serveur avec les serveurs NTP de rang supérieur,
  * Et un mode symétrique avec les serveurs NTP de même rang.
* Puis, les clients terminaux (serveurs et ordinateurs) vont alors se synchroniser en mode client/serveur ou en multicast en vue de recevoir un horodatage exact. 

*Note: Toutefois, la présence de ces clients sur le réseau n’est autorisée qu’à partir du troisième niveau.*
### Modes NTP
Mode symétrique actif
Un serveur fonctionnant dans ce mode envoie périodiquement des messages, sans se soucier de l’état de ses voisins (joignables, serveurs primaires ou secondaires, clients). Il indique ainsi sa « volonté » de synchroniser d’autres serveurs et d’être synchroniser par eux.
Mode symétrique passif
Ce type d’association est généralement créée lors de l’arrivée sur le serveur d’un message d’un autre serveur (en mode symétrique actif). Le serveur annonce son intention de synchroniser et d’être synchronisé. Seulement il ne le fait qu’après avoir été sollicité par un autre serveur. 
Mode client
La machine envoie des messages régulièrement, sans se préoccuper de l’état de ses voisins. La station (typiquement, elle appartient à un réseau LAN) indique ainsi sa « volonté » d’être synchronisée.
Mode serveur
L’association de ce mode est créée lors de la réception d’une requête (un message) d’une station en mode client.
Mode broadcast
Destiné aux réseaux locaux, il se limite à une diffusion d’informations horaires pour des clients pouvant être soit passifs, soit découvrant ainsi les serveurs avec lesquels ils vont se synchroniser.

## Avantages et utilisations de NTP
* Donner la meilleure précision possible compte tenu des conditions matérielles
* Résister à une multitude de pannes (y compris les bugs d’implémentation)
* Réaliser l’authentification cryptographique basée sur des infrastructures de clefs symétriques et de clefs publiques

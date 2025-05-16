# Shelly-Pro3em-cloud-and-local

Lit les mesures des 3 phases (tension, courant, puissance, énergie, etc.).

Publie les mesures localement via MQTT.

Envoie les données dans le cloud (via HTTP ou MQTT vers un serveur distant).

Peut fonctionner avec 1, 2 ou 3 phases actives.

Est adaptable selon ton besoin.



---

1. Pré-requis

MQTT activé sur ton Shelly.

Une destination cloud configurée (ex. : une URL HTTP ou un broker MQTT distant).

Adapter le script si tu veux n’utiliser que certaines phases ou un seul type de sortie (local ou cloud).

Explication

Le script lit les données des 3 phases.

Il envoie les mesures toutes les 60 secondes.

Il publie localement sur un broker MQTT (ex. : HiveMQ ou Mosquitto en local).

Il peut aussi envoyer dans le cloud :

Soit via HTTP POST vers ton serveur Web.

Soit via MQTT (si tu mets cloud_via_http = false).




Personnalisation possible

N'utiliser que 2 phases ? => change la boucle for (let i = 0; i < 2; i++)

Ajouter les puissances apparentes, réactives, etc. => adapte Shelly.getComponentStatus.

# Tableau de bord de projets

Application NuxtJS de gestion de projets pour indépendants. Réalisé avec [Vue Material](https://vuematerial.io/).

## Fonctionnalités
* Création et modification de projets
* Création et modification de clients
* Actions rapides depuis le tableau de bord

### Installation

1. Cloner le projet et installer les dépendances.

``` bash
git clone https://github.com/isaac-gros/dvic-monitoring.git
cd dvic-monitoring
npm install
```

2. Ajouter les paramètres de configuration d'une application [Firebase](https://console.firebase.google.com/) au fichier `.env.example`. Une fois la configuration terminée, renommer le fichier `.env.example` en `.env`. Note : Les outils Analytics pour Firebase n'ont pas été activés pour ce projet.

``` conf
# Firebase configuration
apiKey=""
authDomain=""
databaseURL=""
projectId=""
storageBucket=""
messagingSenderId=""
appId=""
```

3. Démarrer le serveur en éxécutant la commande suivante

``` bash
npm run dev
```

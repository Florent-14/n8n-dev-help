# Avertissement

>Je ne suis pas un expert de l'écosystème Meta. Ce document présente la procédure que j'ai suivie pour automatiser la création de publications sur Facebook, Instagram et Threads. Les informations présentées peuvent être incomplètes ou contenir des erreurs.
>
>L'API Graph de Meta et ses interfaces utilisateur évoluent constamment. Les procédures décrites ici, valables en juin 2025, pourront devenir obsolètes dans le futur.

# Table des matières
```table-of-contents
title: 
style: nestedList # TOC style (nestedList|nestedOrderedList|inlineFirstLevel)
minLevel: 0 # Include headings from the specified level
maxLevel: 0 # Include headings up to the specified level
includeLinks: true # Make headings clickable
hideWhenEmpty: false # Hide TOC if no headings are found
debugInConsole: false # Print debug info in Obsidian console
```
# Introduction : Automatiser vos publications Facebook avec n8n

Ce guide qui se veut complet vous accompagne dans la création d'une automatisation pour publier sur Facebook via n8n. Vous apprendrez à configurer l'accès aux API Meta et à créer des nœuds automatisés pour gérer vos publications sur les pages Facebook.
## Sources 

* https://community.n8n.io/t/step-by-step-guide-how-to-post-to-instagram-via-n8n-automations/61665
* https://www.youtube.com/watch?v=nLlTQRuc2Cg
* https://docs.n8n.io/integrations/builtin/credentials/facebookapp/
* https://developers.facebook.com/docs/graph-api/get-started
* https://developers.facebook.com/docs/permissions
* https://claude.ai/share/5d1cb152-7e1e-446d-939c-5843a35fb6e8

## Étapes principales du processus :

1. **Préparation des comptes**
    - Création d'une adresse email dédiée
    - Création d'un compte Facebook
    - Création d'une page Facebook (minimum 1h après création du compte)
      
2. **Configuration de l'application Meta**
    - Création d'une app Meta sur developers.facebook.com
    - Configuration du type "Entreprise" et du portefeuille business
    - Activation des permissions nécessaires (Facebook Login for Business)
      
3. **Paramétrage des autorisations**
    - Configuration des permissions : `pages_manage_posts`, `business_management`, `page_manage_metadata`
    - Mise en mode "Live" de l'application
    - Test via l'Explorateur de l'API Graph
      
4. **Génération et extension des tokens**
    - Création du token d'accès à la page
    - Extension de la durée de vie (de quelques heures à 2 mois)
    - Récupération du token final
      
5. **Intégration dans n8n**
    - Configuration des credentials Facebook Graph API
    - Paramétrage des nœuds pour différents types de publications
    - Test des workflows

## Types de publications supportées :

- **Messages texte simples**
- **Liens vidéo** (YouTube fonctionne parfaitement, Instagram avec limitations)
- **Images** via URL

Ce processus peut sembler complexe au premier abord, mais une fois configuré, il vous permettra d'automatiser efficacement vos publications Facebook depuis n8n !

## Important

>Il ne sera pas possible avec cette configuration de poster sur un **compte personnel** mais seulement sur une **Page Facebook**. L’API Graph ne permet pas cela à ce jour.

# Créer une adresse email

1. Aller sur Hotmail, Gmail, etc. pour créer l’adresse mail du compte qui servira à la gestion des réseaux sociaux

# Créer un compte Facebook

1. Créer un compte Facebook
2. Créer une page Facebook (cela n’est possible qu’au moins 1h après la création du compte)

# Créer une app Meta

1. Aller sur l’adresse https://developers.facebook.com/apps/ et cliquer sur "Créer une app"

![[Pasted image 20250527103630.png]]

4. Entrer un nom d’application et l’adresse email créée
5. Pour les cas d’utilisation, choisir "Autre"

![[Pasted image 20250527104159.png]]

6. Sélectionner Type : Entreprise

![[Pasted image 20250527104233.png]]

7. Choisir un "Portefeuille business" **==qui aura été créé au préalable==**

![[Pasted image 20250527104443.png]]

Une fois cela fait, vous avez créé une app et vous pouvez ajouter les produits à celle-ci (Instagram, etc.)

![[Pasted image 20250527104620.png]]

## Changer le nom d'une app Meta/Facebook

### Comment procéder :

1. **Accédez au Meta Developers Dashboard** :
    - Allez sur [https://developers.facebook.com/](https://developers.facebook.com/)
    - Connectez-vous avec votre compte Facebook
    - Sélectionnez votre application
      
2. **Modifiez le nom d'affichage** :
    - Dans le tableau de bord de votre app, allez dans **"Settings"** (Paramètres)
    - Puis **"Basic"** (Paramètres de base)
    - Vous pouvez modifier le "Display Name" dans cette section
      
3. **Soumettez pour révision** :
    - Après avoir changé le nom d'affichage, vous devez soumettre votre app pour révision par Facebook
    - Le nouveau nom ne sera visible aux utilisateurs qu'après approbation de Facebook

### Points importants à retenir :

- **Révision obligatoire** : Si votre app est dans l'App Center, vous devez resoumettre les détails de l'app pour approbation après le changement - sinon l'ancien nom sera utilisé
  
- **Délai d'application** : Il peut y avoir un délai d'environ une semaine après l'approbation avant que le nouveau nom soit effectif partout
  
- **Restrictions de noms** : Certains termes comme "Facebook" ne sont pas autorisés dans les noms d'applications

### Pour n8n et l'API Instagram Graph :

>Le changement de nom n'affectera pas le fonctionnement de votre intégration n8n, car les connexions utilisent l'**App ID** et les **tokens d'accès**, qui restent inchangés même après un changement de nom.

Le processus est gratuit mais nécessite une approbation de Meta/Facebook avant que le nouveau nom soit visible publiquement.

# Configurer les permissions de l’App

1. Pour pouvoir poster sur votre page Facebook, choisissez "Facebook Login for Business" et cliquez sur "Configurer".

![[Pasted image 20250527104811.png]]

Ignorez le message qui vous dit que vous avez besoin d’un accès Avancé :

![[Pasted image 20250527104951.png]]

2. À la place, scroller jusque en bas de la page et "Enregistrer les modifications".
3. Allez sur les "Paramètres de l’app > Général"
4. Dans les paramètres généraux, entrez une "URL de politique de confidentialité" ; si vous n’en avez pas, entrez n’importe quelle URL ("https://mywebsite.com/privacy-policy"). ==Ce paramètre est nécessaire pour configurer l’app en "Live"== (voir ci-dessous)

![[Pasted image 20250527105507.png]]

5. Enregistrer les modifications
6. En haut de la page, vous trouverez le "Mode de l’application" ; cliquez sur le toggle pour mettre l’application en mode "Live"

![[Pasted image 20250527105703.png|320]]

7. Passez la souris sur "Outils" dans la barre de menu supérieure (Meta > Espace App) et cliquez sur "Explorateur de l’API Graph"

![[Pasted image 20250527105903.png|350]]

Cela ouvre une interface de test pour les appels de l’API Meta (Graph).

8. Dans le menu de droite, sélectionnez sous "Application Meta", votre application en cours

![[Pasted image 20250527110130.png]]

9. Sous "Autorisations", sélectionnez les suivantes :
	1. `pages_manage_posts`
	2. `business_management` (information incertaine ici mais il semblerait que ce soit nécessaire si votre page est managé par un Portefeuille business Meta)
	3. `page_manage_metadat` (autorisation ajoutée post-rédaction)

![[Pasted image 20250527110336.png]]

À ce moment, nous avons donné les droits à l’app de gérer les posts sur une page Facebook.

![[Pasted image 20250527110626.png]]

>Vous pouvez également ajouter d’autres droits si nécessaire pour votre cas d’utilisation.

### Modification des droits d’accès

#### Générer un nouveau token d’accès utilisateur
Comme vous avez modifié la portée du token d’accès, vous devez en créer un nouveau. Cliquez sur **Générer un token d’accès**. Comme pour la première requête, vous devez autoriser votre application à accéder à votre e-mail dans la boîte de dialogue Facebook Login.

Une fois le nouveau token créé, cliquez sur **Envoyer**. Cette fois, tous les champs de votre requête seront renvoyés.

# Récupérer le token d’accès

## Un token, c’est quoi ?

Un token d’accès est une chaîne opaque qui identifie un utilisateur ou une utilisatrice, une application ou une Page, et que l’application peut utiliser pour passer des appels à l’API Graph. Le token contient notamment sa date d’expiration et le nom de l’application qui l’a généré. La plupart des appels d’API sur les applications Meta doivent contenir un token d’accès, en raison des contrôles de confidentialité. Il existe différents types de tokens d’accès correspondant à divers cas d’utilisation, et différentes méthodes pour obtenir ces tokens d’accès.

Les tokens d’accès utilisateur·ice se présentent sous deux formes : les tokens de courte durée et ceux de longue durée. Généralement, les tokens de courte durée sont valides pendant une heure ou deux, tandis que les tokens de longue durée sont valides pendant environ 60 jours. Notez que la durée de vie d’un token peut varier sans préavis ou expirer de manière anticipée.

Les tokens d’accès générés par une connexion web sont de courte durée, mais vous pouvez les convertir en tokens de longue durée.

## Procédure

1. Sous "Utilisateur ou Page", sélectionnez "Obtenir un token d’accès de Page"

![[Pasted image 20250527111357.png]]

2. Meta vous demandera de confirmer que vous souhaitez vous connectez à Facebook en tant que vous-mêmes

![[Pasted image 20250527112623.png|350]]

3. Sélectionnez la ou les pages auxquelles vous souhaitez que l’app se connecte et confirmer

![[Pasted image 20250527112751.png|350]]

![[Pasted image 20250527112839.png|350]]

Deux autres droits ont automatiquement été ajoutés.

![[Pasted image 20250527113300.png|350]]

4. Maintenant, nous allons tester cette configuration en faisant un appel API de test au moyen de la commande `me/accounts` dans la barre de test d’url

![[Pasted image 20250527113529.png]]

Cette commande devrait vous donner la liste des comptes que vous pourrez gérer avec ce token.

>Il se peut cependant que vous obteniez le résultat suivant :

![[Pasted image 20250527113725.png]]

5. Dans ce cas, relancez la génération du token. Cette seconde demande de permissions inclut cette fois une demande d’accès à l’application Entreprises

![[Pasted image 20250527113912.png|350]]

![[Pasted image 20250527113945.png|350]]

6. Un nouveau token est généré. Une fois cela fait, relancez la commande avec `me/accounts` et le résultat devrait être bon

![[Pasted image 20250527114333.png]]

7. Vous pouvez maintenant récupérer le token d’accès à la page Facebook sur laquelle vous souhaitez poster : `access_token`

> IMPORTANT : ce token n’a une durée de vie que de quelques heures…

# Comment étendre la durée de vie d’un token Facebook ?


1. Sur la barre de menu principale, passez la souris sur "Outils" puis cliquer sur "Outils de débug de token d’accès"

![[Pasted image 20250527115157.png]]

2. Entrez votre token dans la zone pour débuguer

![[Pasted image 20250527115324.png]]

3. Vous avez accès aux informations du token. Cliquez sur "Étendre le token d’accès" une première fois

![[Pasted image 20250527115524.png]]

4. Vous obtenez un nouveau token d’accès d’une durée de 2 mois

![[Pasted image 20250527115654.png]]

5. Cliquez sur "Débuguer" pour accéder aux informations du token ; la durée de vie est d’effectivement 2 mois

![[Pasted image 20250527120029.png]]


# Créer une connexion Facebook dans n8n

1. Créer un nouveau "Credential" ; choisissez le type "Facebook Graph API"

![[Pasted image 20250527120651.png|350]]

2. Entrer le token "étendu" dans la fenêtre de configuration et sauvez

![[Pasted image 20250527121107.png]]

3. La connexion fonctionne 🥳

![[Pasted image 20250527121132.png]]

4. Ajoutez un nœud "Facebook Graph API" dans votre workflow
	1. Credential : la connexion que vous venez de créer avec le token étendu
	2. Host URL : Default
	3. HTTP Request Method: GET ou POST en fonction de ce que vous souhaitez faire (POST pour poster un message)
	4. Graph API Version : la dernière
	5. Node : me
	6. Edge : feed

## Valeurs des params de Facebook Graph API
### /me
Le nœud `/me` est un point de terminaison spécial qui se transforme en ==ID d’objet de la personne ou la Page dont le token d’accès est utilisé pour effectuer les appels d’API==.

>Utiliser `/me` avec le token d’accès permet de ne pas à avoir à spécifier l’ID du user ou de la page.

### Arêtes (Edge)
Une arête est une connexion entre deux nœuds. Par exemple, un nœud d’utilisateur peut être connecté à des photos, et un nœud de photo peut être connecté à des commentaires.

## Types de nœuds de l'API Graph de Facebook dans n8n

Voici les types de nœuds (nodes) principaux de l'API Graph de Facebook que vous pouvez utiliser dans n8n.

Dans n8n, le nœud Facebook Graph API vous permet d'opérer sur des "nodes" spécifiques, par exemple `/<page-id>/feed` [Facebook Graph API node documentation | n8n Docs](https://docs.n8n.io/integrations/builtin/app-nodes/n8n-nodes-base.facebookgraphapi/). Voici les principaux types de nœuds disponibles :

### Nœuds principaux :

1. **User** - Représente un utilisateur Facebook
2. **Page** - Représente une page Facebook (entreprise, organisation, etc.)
3. **Post** - Représente une publication sur Facebook
4. **Comment** - Représente un commentaire sur une publication
5. **Photo** - Représente une photo uploadée
6. **Album** - Représente un album photo (Note: la création d'albums a été dépréciée depuis l'API v7.0+)
7. **Video** - Représente une vidéo
8. **Event** - Représente un événement Facebook
9. **Group** - Représente un groupe Facebook

### Edges (connexions, arêtes) courantes :

Les "edges" représentent des collections d'objets attachées à un nœud [Facebook Graph API node documentation | n8n Docs](https://docs.n8n.io/integrations/builtin/app-nodes/n8n-nodes-base.facebookgraphapi/). Exemples typiques :

- **feed** - Flux de publications d'une page ou utilisateur
- **posts** - Publications d'un objet
- **comments** - Commentaires sur un objet
- **photos** - Photos d'un album ou utilisateur
- **reactions** - Réactions (like, love, etc.) sur un objet
- **insights** - Statistiques d'une page

### Types de publications (status_type) :

Pour les publications dans un feed, vous pouvez rencontrer ces types : mobile_status_update, created_note, added_photos, added_video, shared_story, created_group, created_event, wall_post, app_created_story, published_story, tagged_in_photo, approved_friend [facebook graph api - What types of posts are in a feed? - Stack Overflow](https://stackoverflow.com/questions/7334689/what-types-of-posts-are-in-a-feed).

### Configuration dans n8n :

Dans le nœud Facebook Graph API de n8n, vous devez spécifier :

- **Node** : Le nœud sur lequel opérer (ex: `/{page-id}/feed`)
- **Edge** : L'edge du nœud (optionnel)
- **HTTP Method** : GET, POST, ou DELETE
- **Graph API Version** : La version de l'API à utiliser

### Exemple d'utilisation :

Pour récupérer les publications d'une page :

- **Node** : `/{page-id}`
- **Edge** : `feed`
- **Method** : GET

Pour poster sur une page :

- **Node** : `/{page-id}`
- **Edge** : `feed`
- **Method** : POST

**Note importante** : Vous devez configurer les bonnes permissions et tokens d'accès selon le type d'opération que vous souhaitez effectuer.

## Poster une url de vidéo

Ajoutez un nœud "Facebook Graph API" dans votre workflow

1. Credential : la connexion que vous venez de créer avec le token étendu
2. Host URL : Default
3. HTTP Request Method: POST
4. Graph API Version : la dernière
5. Node : me
6. Edge : feed
7. Options > Query Parameters
	1. message : texte de votre message
	2. link : url de la vidéo

![[Pasted image 20250527183445.png]]

Vous obtiendrez :

![[Pasted image 20250527184001.png]]

>IMPORTANT : cela ne fonctionne PAS pour les vidéos Instagram. Le post est créé, le texte s’affiche mais le lien n’est pas ajouté 

![[Pasted image 20250527183526.png]]

Pour obtenir un "certain" résultat avec un lien Instagram, il faut inclure l’url dans la valeur du paramètre "message". Cependant, la vidéo ne sera pas _embedded_ dans le post comme une vidéo YouTube l’est ; à la place, vous n’aurez que le lien.

![[Pasted image 20250527183834.png]]

Si quelqu’un sait comment résoudre ce problème, je suis preneur 😅

## Poster l’url d’une image

Pour poster une image, il faudra le paramétrage suivant :
1. Credential : la connexion que vous venez de créer avec le token étendu
2. Host URL : Default
3. HTTP Request Method: POST
4. Graph API Version : la dernière
5. Node : me
6. Edge : photos
7. Options > Query Parameters
	1. message : texte de votre message
	2. url : url de l’image

![[Pasted image 20250527184353.png]]

![[Pasted image 20250527184426.png]]


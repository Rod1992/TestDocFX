# Avant de builder
A partir du 01/08/2019, les applications doivent proposer le format 64 bits. A vérifier dans les Player settings:
  
![Target 64 bits architectures](https://user-images.githubusercontent.com/18393283/59424242-eddfe500-8dd3-11e9-92be-f777c4ac9c57.png)

# Build

Pour les versions, de dev et de préprod, il est possible de builder via [Unity Cloud Build](https://developer.cloud.unity3d.com/build/orgs/regatta/projects/virtual-regatta-inshore)

Dans la configuration, la différence entre une build dev et prod se situe au niveau des define symbols:
- FORCE_PRODUCTION pour la production
- Vide pour le dev

Pour builder en local, il faut utiliser dans les publishing settings les infos contenues dans [VRI-sign](https://github.com/virtualregatta/VRI2K16/files/3291248/Sign-VRI.zip)

**Attention: Ne jamais pusher les project settings avec le define symbol FORCE_PRODUCTION**.

# Version de production pour Play Store
Pour la version de production destinée au Play Store, il faut dorénavant utiliser les Android App bundles pour limiter le poids de l'app suite au passge au 64 bits.
Ce n'est pas encore supporté par UCB, ça doit donc être fait en local en vérifiant de bien être en configuration prod (FORCE_PRODUCTION):
![Android App Bundles](https://user-images.githubusercontent.com/18393283/59424251-f0dad580-8dd3-11e9-9796-6875b6698817.png)

# Soumission sur le Google Play Store
Direction la [Google Play Console](https://play.google.com/apps/publish/?account=7870240511899331134#). 
![](https://user-images.githubusercontent.com/18393283/59435989-0d383b80-8def-11e9-996b-dbc7e532aba1.png)  
Une fois dans l'app et le menu Gestion de la publication > Versions de l'application, il est possible de créer/modifier la nouvelle version.    
Il faut que le build number soit incrémenté si un nouveau package est déposé. Sinon, il est possible de supprimer le doublon dans la bibliothèque d'artefacts.  
Ne pas oublier de proposer des traductions pour le changelog.
Une fois la version enregistrée et verifiée, le deploiement se fait en quelques heures.  
**Attention à bien déployer sur 100% des devices.**

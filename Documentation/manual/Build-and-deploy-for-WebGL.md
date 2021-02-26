# Build
L'integralité des builds WebGL peuvent se faire sur [Unity Cloud Build](https://developer.cloud.unity3d.com/build/orgs/regatta/projects/virtual-regatta-inshore/)
Les points à vérifier dans la configuration des builds concernent la branche git utilisée et la version de Unity.  
![](https://user-images.githubusercontent.com/18393283/59436434-e4647600-8def-11e9-8777-979f54eaf7bf.png)

# Deploy
Le déploiement s'effectue via un script sur une machine de dev.  
Il faut se connecter en ssh sur ubuntu@dev.virtualregatta.com:22.  
Une fois connecté, aller dans le répertoire **/data/vri2k17**.
Le script actuel est deploy.sh et s'utilise comme suit:  
`./deploy_wp.sh [-p] [-v "versionName"]`  
**-p** permet de preciser si l'on veut déployer sur l'environnement de production  
**-v** permet de donner un nom spécifique à la version récuperée. Par défaut il s'agit de la date au format YYYMMDD suivi numéro de build UCB séparé par un tiret (signe moins). Il est préférable de **ne pas** spécifier de nom de version, mais si jamais, essayer de respecter le même format en la suffixant, par exemple: 20190802-425-montest. 
Le script récupere ensuite la dernière build UCB prod ou dev selon le param p et l'upload sur la machine concernée.  
A cet instant la build est visible sur l'url de preprod:  
https://www[-dev].virtualregatta.com/fr/inshore-jeu/[?versionRelease=[versionName]]  
Comme par exemple:
- Pour dev: [https://www-dev.virtualregatta.com/fr/inshore-jeu/?versionRelease=20190612-184](https://www-dev.virtualregatta.com/fr/inshore-jeu/?versionRelease=20190612-184)    
- Pour la preprod: [https://www.virtualregatta.com/fr/inshore-jeu/?versionRelease=20190612-184](https://www.virtualregatta.com/fr/inshore-jeu/?versionRelease=20190612-184)  
- Pour la prod: [https://www.virtualregatta.com/fr/inshore-jeu/](https://www.virtualregatta.com/fr/inshore-jeu/)  

Une fois validée, pour que l'accès soit possible pour tous il faut mettre à jour la configuration du portail: 
https://www.virtualregatta.com/wp-login.php (ou https://www-dev.virtualregatta.com/wp-login.php pour dev)
Une fois identifié, modifier la "Version de la release WebGL de VRI" en conséquence.

Et puisque la version WebGL sera toujours live avant celles mobiles, penser à informer (via slack ou autre) le reste de la team (comm. notamment) dés que ce déploiement a eu lieu.

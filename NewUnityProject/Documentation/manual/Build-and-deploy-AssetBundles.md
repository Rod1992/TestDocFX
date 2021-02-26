Les AssetBundles sont utilisés principalement pour les bateaux et les décors de VRI afin de pouvoir les sortir de la build principale et de les mettre à jour sans avoir à créer une nouvelle version de l'app.

# Build
![](https://user-images.githubusercontent.com/18393283/59490979-3bb52580-8e86-11e9-9d31-7f0c7268f1c3.png)  
Dans un premier temps, pour tester les asset bundles dans l'éditeur, il faut utiliser le mode Simulation qui ne nécessite pas de les builder.  
Une fois validé, il faut les builder avec le menu Asset/AssetBundles/Build AssetBundles pour chaque plate-forme (au moins iOS, Android et WebGL)

# Deploy
Il faut dans un premier temps déposer les assetBundles sur la machine dev.virtualregatta.com dans le répertoire /data/vri2k17/AssetBundles via un logiciel de ftp (FileZilla, WinSCP).  
Ensuite, se connecter en ssh sur ubuntu@dev.virtualregatta.com:22 et se déplacer dans le dossier **/data/vri2k17**.  
Le déploiement se fait sur Amazon S3, il faut donc utiliser le script bash s3_deploy.sh :  
`./s3_deploy.sh -ab [-p] [-v "versionName"]`  
**-ab** pour préciser que l'on souhaite déployer les assetBundles  
**-p** permet de préciser si l'on veut déployer sur l'environnement de production  
**-v** permet de donner un nom spécifique à la version récupérée. Par défaut il s'agit de la date du jour au format YYYYMMDD. Il est préférable de laisser le nom par défaut, mais si c'est nécessaire, préférez un nom au même format avec un suffixe, exemple: 20190802-montest.

Pour pouvoir utiliser ces assets avec l'application courante, direction l'[admin de Gamesparks](https://portal2.gamesparks.net/games/c305927PJZ4i).  
Ensuite dans Manage>Settings:  
![](https://user-images.githubusercontent.com/18393283/59492036-5092b880-8e88-11e9-8a3c-fd4ba72c863d.png)  
Attention de bien choisir Preview(dev) ou Live(prod).  
Les AssetBundles sont renseignés en bas avec un détail par plateforme et par version de l'app.


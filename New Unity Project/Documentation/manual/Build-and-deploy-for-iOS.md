# 1. Build
## Unity
Depuis la machine de build: 
* Pull de la branche de la version concernée via Github Desktop, SourceTree ou autre
* S’assurer que les numéros de build et de version sont correctement incrémentés: le numéro de version doit être > au dernier uploadé sur l’App store Connect
* S’assurer que vous buildez bien depuis une version PRO d’Unity et que le splash screen est bien configuré dans les player settings.
* Vérifier que l’on est en production en lançant le jeu depuis Unity 
  * Si ce n’est pas le cas, ajouter FORCE_PRODUCTION aux Scripting Define Symbols (Player Settings). Peut être ajouté via Utils > Switch Mode > Prod.
  * Vérifier à nouveau en lançant le jeu
* Builder depuis Unity.

## XCode
* Ouvrir **%build_folder%/Unity-iPhone.xcworkspace**

* Valider/corriger les project settings tel que le propose Xcode dans le panneau accessible en cliquant l’icône Warning (par défaut dans la barre en haut à gauche de la fenêtre): 
  * Pods project : Validate Project Settings / Update to recommended settings
  * Unity-iPhone project : Validate Project Settings / Update to recommended settings

* Sur le projet (root dans l’explorateur de projet et target Unity_iPhone), se rendre dans General>Identity vérifier le numéro de version et de build.  
![](https://user-images.githubusercontent.com/18393283/59432940-c9dace80-8de8-11e9-9da7-adae81a13fde.png)  
![](https://user-images.githubusercontent.com/18393283/59432950-cc3d2880-8de8-11e9-91ea-f885650545ad.png)  
* Dans Capabilities, activer Background Modes (dans lequel Remote notification doit être coché), Push Notifications, In-App Purchase et Associated Domains (avec l'entrée "applinks:*.virtualregatta.com") :  
![](https://i.imgur.com/KJfBCPe.png)
* Vérifier dans Info que les deux champs concernant la Privacy sont renseignés tels qu’ajoutés précédemment dans le fichier Info.plist.  
![](https://user-images.githubusercontent.com/18393283/59432977-d5c69080-8de8-11e9-86f3-02d5b6a9bbc0.png) 
* Builder et archiver à l’aide du menu Product>Archive. Une fois l’archivage terminé, l’Organizer devrait s’ouvrir avec l’archive nouvellement créée sélectionnée.  
![](https://user-images.githubusercontent.com/18393283/59432978-d5c69080-8de8-11e9-88d3-ff7e44367193.png)  
Si ce n’est pas le cas, l’Organizer s’ouvre depuis le menu Window>Organizer  
![](https://user-images.githubusercontent.com/18393283/59432980-d5c69080-8de8-11e9-8fbd-e6d3a2c5760c.png)  
* Pour uploader vers l’App Store depuis l’Organizer, cliquer sur “*Distribute App*” à droite et suivre la procédure en laissant tout par défaut et en auto-signant de préférence.  
![](https://user-images.githubusercontent.com/18393283/59432981-d65f2700-8de8-11e9-95ef-72e2a6adb9fb.png)  
![](https://user-images.githubusercontent.com/18393283/59432982-d65f2700-8de8-11e9-94fb-0ecb2325de59.png)  
* Patienter
* Garder les différentes options telles quelles > Next
* Upload

# 2. Publication App Store
## Testflight
Sur l’[App Store connect](https://appstoreconnect.apple.com/), aller dans Mes Apps > [App] > Testflight  
Constater que la version est en cours de vérification.  
Une fois que c’est terminé, un warning “Attestations manquantes” s’affiche:  
* Cliquer sur le signe warning, puis soumettre les attestations pour l’exportation
* Répondre non puis commencer les tests internes
* Valider la version sur l’iPad

## Création de la version
* Dans l'AppStore: (+) VERSION OU PLATE-FORME
* Remplir le champ “Nouveautés de cette version” dans toutes les langues.
* Vérifier les visuels et en ajouter/supprimer si nécessaire.
* Dans build, rajouter la build souhaitée.
* Faire attention au mode de publication: auto ou manuel
* Valider et répondre Non sur l’identifiant publicitaire: Envoyer
* Si publication manuelle, attente du mail de confirmation d’Apple
* Enjoy!

# Localisation  
Client:   
- Git: _https://github.com/virtualregatta/VRI2K16_
- Chemin: _proto/src/client/unity/Assets/Scripts/Simulation/Boat/Engine/BoatEngine.cs_
  
Serveur:  
- Git: _https://github.com/virtualregatta/VRI2k16-Photon-Server_
- Chemin: _src/server/VRI2k16.Common/Simulation/Engine.cs_  
 
# Structure

Le boat engine de VRI prend en entrée un delta temps entre chaque frame de calcul (float, en secondes), un boat state initial passé en référence (BoatState,modifié et tenant lieu de retour), et une configuration du bateau (BoatConfig).

La fonction principale d’exécution est la suivante:  

`public BoatState Run(float deltaTimeS, ref BoatState boatState, BoatConfig boatConfig)`

Les 6 composantes de la dynamique du bateau sont exécutées selon un ordre défini. Les commandes dans le Run sont les suivantes:  

1. Déployer une voile le cas échéant (spi ou foc, via action joueur).
1. Appliquer une pénalité le cas échéant (qui influe sur la vitesse du bateau, via le calcul des règles de régate).
1. Choquer les voiles le cas échéant (via action joueur).
1. Calculer la vitesse de rotation du bateau (via action joueur).
1. Calculer la vitesse d’avancement du bateau.
1. Calculer la vitesse de dérive le cas échéant (via la configuration du bateau).

Chaque exécution est également dépendante du contenu du boat state de référence (état précédent du bateau, BoatState).  
Le Run du bateau doit être appelé à intervalle régulier, plusieurs fois par seconde (par défaut 30 fois par seconde, soit un delta temps de 33 millisecondes, cette valeur étant configurable en modifiant le EngineStepMs dans le BoatConfig).  

# Détail des exécutions
_(1.) Déployer une voile le cas échéant (spi ou foc, via action joueur)._

`private void _HandleSailActive(float deltaTimeS, ref BoatState boatState, BoatConfig boatConfig, BoatModification boatModification)`

Cette fonction permet de:
- démarrer le décompte pour le changement de voile (_BoatConfig.Sails.RemainingSwitchTimeS_) en cas d’action joueur (_BoatState.Action.SailSwitchToGennaker_), si un changement de voile n’est pas déjà en cours.
- Faire le décompte (en utilisant le delta temps deltaTimeS) et exécuter le changement de voile en fin de décompte (_BoatState.Sails.IsInGennaker_).

_(2.) Appliquer une pénalité le cas échéant (qui influe sur la vitesse du bateau, via le calcul des règles de régate)._

`private void _ComputePenalty(float deltaTimeS, ref BoatState boatState, BoatConfig boatConfig)`

Cette fonction permet de:
- Calculer un facteur d'influence de la pénalité sur la dynamique globale du bateau (_BoatState.Penalty.Ratio_), 0 pour une influence totale et 1 pour une influence nulle (suivant _BoatState.Penalty.IsActive_).
- Le facteur converge vers sa valeur cible suivant les les valeurs d’amortissement passées en configuration (_BoatConfig.Penalty.AccelerationDamping_, et_ BoatConfig.Penalty.DecelerationDamping_).

_(3.) Choquer les voiles le cas échéant (via action joueur)._

`private void _ComputeTrim(float deltaTimeS, ref BoatState boatState, BoatConfig boatConfig)`

Cette fonction permet de:
- Calculer un facteur d’influence du choqué des voiles sur la dynamique globale du bateau (_BoatState.Trim.Ratio_), une valeur min >= 0 pour une influence totale et 1 pour une influence nulle (suivant _BoatState.Action.Trim_).
Le facteur converge vers sa valeur cible suivant les valeurs d’amortissement passées en configuration (_BoatConfig.Trim.AccelerationDamping_, et _BoatConfig.Trim.RatioMutiplicatorOverTwa_).
Note: la valeur du facteur d’influence totale suit une courbe fonction du TWA, pour un retour entre 0 et 1. Cette courbe est pour l’heure commune à tous les bateaux et n’a pas vocation à être modifiée.  
  
_(4.) Calculer la vitesse de rotation du bateau (via action joueur)._

`private void _ComputeRotation(float deltaTimeS, ref BoatState boatState, BoatConfig boatConfig, BoatModification boatModification)`

Cette fonction permet de:
- Gérer le “mode panique” (rotation forcée appliquée instantanément, la valeur est définie dans _BoatConfig.Rotation.PanicModeHeadingDegrees_)
Note: le “mode panique” est accessible au joueur quand il est bout au vent depuis un certain temps et que la vitesse angulaire de son bateau est faible.
- Calculer la vitesse de rotation cible en fonction des inputs et de l’état précédent du bateau (_BoatState.Action.XAxis_ et _BoatState.Action.TapCheck_).
Note 1: Le tap check est utilisé durant un certain laps de temps (défini et commandé par le client) pour permettre au bateau de virer degré par degré (TWA). La vitesse de rotation cible est fixée à une valeur fixe assez faible définie en configuration (_BoatConfig.Rotation.TapForcedSpeed_).
Note 2: La vitesse d’avancement du bateau a une influence sur la vitesse de rotation du bateau, se basant sur une vitesse d’avancement référence définie en configuration (_BoatConfig.Rotation.AdvanceSpeedRefKnots_).
- Faire converger le bateau vers un angle cible (TWA ou cap, _BoatState.Action.AngleTarget_) le cas échéant (auto TWA activé ou auto tack enclenché).
- Calculer la vitesse de rotation du bateau (_BoatState.Rotation.Speed_) et son cap (_BoatState.Rotation.HeadingDegrees_).
  
_(5.) Calculer la vitesse d’avancement du bateau._

`private void _ComputeAdvance(float deltaTimeS, ref BoatState boatState, BoatConfig boatConfig, BoatModification boatModification)`

Cette fonction permet de:
- Calculer le TWA.
- Calculer la vitesse cible (dépendante du facteur d’influence sur une éventuelle pénalité et/ou du facteur d’influence sur le choqué des voiles).
Note: la vitesse cible est définie par la polaire du bateau présente en configuration (vitesse en fonction du TWA et de la voile, _BoatConfig.Sails.Gennaker.Vpps_ et _BoatConfig.Sails.Jib.Vpps_).
- Faire converger la vitesse du bateau vers la vitesse cible (l’accélération et la décélération étant définie par un amortissement fonction du TWA).
Note: cette fonction est présente pour affiner les comportement général du bateau, il n’est pas forcément nécessaire de la modifier pour régler l’accélération/la décélération du bateau, mieux vaut modifier les valeurs d’amortissement (_BoatConfig.Advance.BaseAccelerationDamping_ et _BoatConfig.Advance.BaseDecelerationDamping_).
- Calculer la position du bateau.
Note: l’avancement est calculé sur l’axe du bateau défini par son cap.
  
_(6.) Calculer la vitesse de dérive le cas échéant (via la configuration du bateau)._

`private void _ComputeDrift(float deltaTimeS, ref BoatState boatState, BoatConfig boatConfig)`

Cette fonction permet de:
- Calculer le facteur d’influence de dérive, 0 pour une influence nulle et 1 pour une influence totale.
Le facteur converge vers sa valeur cible suivant les les valeurs d’amortissement passées en configuration (_BoatConfig.Drift.AccelerationDamping_, et _BoatConfig.Drift.DecelerationDamping_).
Note: la dérive se déclenche quand la vitesse d’avancement du bateau passe en dessous d’un seuil de vitesse défini en configuration (_BoatConfig.Drift.SpeedKnotsThreshold_).
- Calculer la vitesse de dérive (_BoatConfig.Drift.SpeedKnots_) en fonction de la force du vent (_BoatState.Wind.TwsKnots_) et d’une vitesse de vent référence arbitraire définie en configuration (_BoatConfig.Drift.WindSpeedReferenceKnots_).
- Calculer la position du bateau (potentiellement modifiée par la dérive)
Note: l’avancement est calculé sur l’axe du vent (TWD, _BoatState.Wind.Degrees_).

# Notes générales
- Les classes d’engine du client et du serveur sont identiques.
- Certaines composantes utilisent les modifications de bateau (_BoatState.BoatModification_) mais celles-ci n’ont aucun impact étant donné que les améliorations de bateaux ne sont plus disponibles dans le jeu. Elle pourraient être retirées.
- Le TWA et le cap sont exprimés en degrés dans l’intervalle [-180, 180]
- Le TWD est exprimé en degrés.
- Les vitesses (avancement, dérive, TWS) sont exprimées en noeuds.

# Démo
[Lien vers la démo](http://pierrerandria.com/BoatTest/)

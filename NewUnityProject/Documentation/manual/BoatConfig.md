# Localisation 
## Client: 
> Git: https://github.com/virtualregatta/VRI2K16  
> Chemin: proto/src/client/unity/Assets/Scripts/Simulation/Boat/Engine/BoatConfig.cs

Serveur:
> Git: https://github.com/virtualregatta/VRI2k16-Photon-Server  
> Chemin: src/server/VRI2k16.Common/Config/BoatConfig.cs 

# Structure
La configuration des bateaux sur VRI est structurée autour des différentes composantes du moteur (BoatEngine), auxquelles viennent s’ajouter d’autres informations qui ne sont pas directement liées au moteur.  
Les données de configuration de bateau sont donc scindées en 10 parties, dont 6 correspondent aux 6 composantes moteurs:  
* Données générales (utilisation diverse, serveur/client)
* Données de contour (utilisées dans le système de collisions, serveur)
* Données de voiles (utilisées par le moteur, serveur/client)
* Données de pénalité (utilisées par le moteur, serveur/client)
* Données du choqué des voiles (utilisées par le moteur, serveur/client)
* Données de rotation (utilisées par le moteur, serveur/client)
* Données d’avancement (utilisées par le moteur, serveur/client)
* Données de dérive (utilisées par le moteur, serveur/client)
* Données de vol (utilisées par le client)
* Données d’améliorations du bateau (dépréciées, précédemment utilisées par le moteur, serveur/client)

# Détail de la configuration
### Données générales (utilisation diverse, serveur/client)

    [XmlElement(“general”)] public GeneralSetup General = new GeneralSetup();

Contient:
* [expert] EngineStepMs: Le delta temps entre deux appels du Run du moteur (par défaut 33 ms)
* BoatId: L’identifiant du bateau
* Name: Le nom du bateau
* HullLengthMeters: La longueur du bateau (hors-tout)
* HullWidthMeters: La largeur du bateau (hors membres)
* VisualScaling: L’échelle de présentation du bateau (par défaut 0.5, soit 50%)
* [expert] UnityTag: Le tag du bateau pour les assets bundles (client)
* [expert] UnityPrefabName: Le nom du prefab du bateau pour les assets bundles (client)
* [expert] SternMaxPercent: Le pourcentage de longueur pour considérer le chevauchement au niveau de la poupe du bateau
* [expert] MatchRaceCourseId: L’identifiant de parcours préférentiel pour les challenges de type Match.
* [expert] TrainingChallengeId: L’identifiant du challenge d'entraînement lié au bateau.
* [expert] PrivateChallengeId: L’identifiant du challenge de courses privées lié au bateau.


### Données de contour (utilisées dans le système de collisions, serveur)

    [XmlElement(“outline”)] public OutlineSetup Outline = new OutlineSetup();

Contient:
* PointsUpwind: Les points définissant le contour du bateau au près (ie en foc).
* PointsDownwind: Les points définissant le contour du bateau au portant (ie en spi).

**Note**: la forme peut changer selon le type de voile déployée (comme par exemple avec un bout dehors amovible/rétractable).



### Données de voiles (utilisées par le moteur, serveur/client)

    [XmlElement(“sails”)] public SailsSetup Sails = new SailsSetup();

Contient:
* [expert] Jib: Les polaires du foc, soit un ou plusieurs tableaux (un pour chaque vitesse de vent arbitrairement définie) de 181 valeurs de vitesse (une par angle TWA, de 0 à 180 inclus).
* [expert] Gennaker: Les polaires du spi, soit un ou plusieurs tableaux (un pour chaque vitesse de vent arbitrairement définie) de 181 valeurs de vitesse (une par angle TWA, de 0 à 180 inclus).
* [expert] HasGennaker: Le flag (bool) définissant si le bateau a ou non un spi.
* SailsSwitchTimeS: Le temps de changement de voile.
* [expert] Flap: Les données de fasseyement (angles TWA seuils, au près et portant pour le spi, au près pour le foc).  

**Notes**:
* Si le bateau n’a pas de spi, le changement de voile ne pouvant pas se faire, les polaires Gennaker seront ignorées.


### Données de pénalité (utilisées par le moteur, serveur/client)

    [XmlElement(“penalty”)] public PenaltySetup Sails = new PenaltySetup();

Contient:
* SpeedRatio: Le facteur de vitesse à appliquer lors d’un pénalité.
* [expert] DecelerationDamping: La valeur d’amortissement pour le calcul du facteur d’influence de la pénalité, au moment de l’application de la pénalité (plus elle est grande, plus le bateau ralentit rapidement)
* [expert] AccelerationDamping: La valeur d’amortissement pour le calcul du facteur d’influence de la pénalité au moment de sortir de la pénalité (plus elle est grande, plus le bateau ré-accélère rapidement)
* DurationS: La durée de la pénalité en secondes.


### Données du choqué des voiles (utilisées par le moteur, serveur/client)

    [XmlElement(“trim”)] public TrimSetup Outline = new TrimSetup();

Contient:
* [expert] SpeedRatio: Le facteur de vitesse à appliquer lors du choqué des voiles (tous les bateaux sont à 0 par défaut)
* [expert] DecelerationDamping: La valeur d’amortissement pour le calcul du facteur d’influence du choqué des voiles, au moment de l’application du choqué des voiles (plus elle est grande, plus le bateau ralentit rapidement).
* [expert] AccelerationDamping: La valeur d’amortissement pour le calcul du facteur d’influence du choqué des voiles, au moment de re-border les voiles (plus elle est grande, plus le bateau ré-accélère rapidement)
* [expert] RatioMultiplicatorOverTwa: Un tableau de 181 valeurs (une par degré TWA entre 0 et 180 inclus) définissant la variation de la valeur maximale d’influence du choqué des voiles en fonction de l'angle au vent.

**Note**: Ces valeurs n’ont pas vraiment vocation à changer d’un bateau à l’autre, mais sont utiles pour avoir un comportement plus crédible sur l’ensemble des bateaux.


### Données de rotation (utilisées par le moteur, serveur/client)

    [XmlElement(“rotation”)] public RotationSetup Rotation = new RotationSetup();

Contient:
* BaseSpeed: La vitesse de rotation cible de référence en degrés/seconde
* [expert] TapForcedSpeed: La vitesse de rotation forcée pour le contrôle au tap degré par degré.
* BaseAccelerationDamping: La valeur d’amortissement pour l’accélération (plus elle est grande, plus l’accélération est forte - pour atteindre une vitesse cible supérieure à celle de l’état de bateau précédent).
* BaseDecelerationDamping: La valeur d’amortissement pour la décélération (plus elle est grande, plus la décélération est forte - pour atteindre un vitesse cible inférieure à celle de l’état de bateau précédent).
* [expert] MinSpeedPercent: Le pourcentage de la vitesse cible de référence définissant la vitesse de rotation minimum du bateau.
* [expert] AdvanceSpeedRefKnots: La vitesse d’avancement référence pour mettre la vitesse de rotation à l’échelle (la vitesse d’avancement du bateau influe sur la vitesse de rotation du bateau).
* [expert] AccelerationDampingRatioOverTwa: Un tableau de 181 valeurs (une par degré TWA entre 0 et 180 inclus) définissant la variation de la valeur maximale d’amortissement pour l’accélération par rapport à l’angle au vent.
* Note: Ces valeurs peuvent être considérées comme constantes, et pour changer significativement la dynamique du bateau, mieux vaut modifier BaseAccelerationDamping.
* [expert] DecelerationDampingRatioOverTwa: Un tableau de 181 valeurs (une par degré TWA entre 0 et 180 inclus) définissant la variation de la valeur maximale d’amortissement pour l’accélération par rapport à l’angle au vent.
* Note: Ces valeurs peuvent être considérées comme constantes, et pour changer significativement la dynamique du bateau, mieux vaut modifier BaseDecelerationDamping.
* [expert] AutoSimulationMaxNumIterations: Le nombre maximum d’itérations pour simuler la rotation automatisée du bateau (pour l’auto TWA et l’auto tack).
* [expert] TwaTargetAngleDegreesStopThreshold: La différence d’angle en deçà de laquelle le bateau se cale sur l’angle cible en cas de rotation automatisée du bateau (pour l’auto TWA et l’auto tack)
* [expert] PanicModeMaxRotationSpeed: (client uniquement) La valeur max de vitesse de rotation en deçà de laquelle le bouton du “mode panique” peut apparaître dans l’interface du jeu en course.
* [expert] PanicModeHeadingDegrees: La valeur de cap sur laquelle se cale instantanément le bateau quand le “mode panique” est enclenché.

**Note**: Cette valeur est en fait ajoutée (ou retirée, en fonction du signe du TWA courant sur [-180; 180]) à celle de l’azimut du vent (TWD) pour définir le cap de sortie.

    boatState.Rotation.HeadingDegrees = (boatState.Wind.TwaDegrees < 0f) ? boatConfig.Rotation.PanicModeHeadingDegrees + boatState.Wind.TwdDegrees : -boatConfig.Rotation.PanicModeHeadingDegrees + boatState.Wind.TwdDegrees;


* [expert] SpeedTresholdForManeuver: (pour les règles uniquement) La valeur seuil de vitesse de rotation du bateau au delà de laquelle le joueur est considéré comme en train de manoeuvrer.



### Données d’avancement (utilisées par le moteur, serveur/client)

    [XmlElement(“advance”)] public AdvanceSetup Advance = new AdvanceSetup();

Contient:
* BaseAccelerationDamping: La valeur d’amortissement pour l’accélération (plus elle est grande, plus l’accélération est forte - pour atteindre une vitesse cible supérieure à celle de l’état de bateau précédent).
* BaseDecelerationDamping: La valeur d’amortissement pour la décélération (plus elle est grande, plus la décélération est forte - pour atteindre un vitesse cible inférieure à celle de l’état de bateau précédent).
* AccelerationDampingRatioOverTwa: Un tableau de 181 valeurs (une par degré TWA entre 0 et 180 inclus) définissant la variation de la valeur maximale d’amortissement pour l’accélération par rapport à l’angle au vent.
* Note: Ces valeurs peuvent être considérées comme constantes, et pour changer significativement la dynamique du bateau, mieux vaut modifier BaseAccelerationDamping.
* DecelerationDampingRatioOverTwa: Un tableau de 181 valeurs (une par degré TWA entre 0 et 180 inclus) définissant la variation de la valeur maximale d’amortissement pour la décélération par rapport à l’angle au vent.
* Note: Ces valeurs peuvent être considérées comme constantes, et pour changer significativement la dynamique du bateau, mieux vaut modifier BaseDecelerationDamping.
* [expert] SpeedFactor: Un facteur de vitesse à appliquer à la vitesse d’avancement en sortie (1 par défaut).



### Données de dérive (utilisées par le moteur, serveur/client)

    [XmlElement("drift")] public DriftSetup Drift = new DriftSetup();

Contient:
* [expert] AccelerationDamping: La valeur d’amortissement pour le calcul du facteur d’influence de la dérive au moment de sortir de dérive, c’est à dire quand on dépasse un certain seuil de vitesse d’avancement du bateau (plus cette valeur est grande, plus le bateau repart rapidement).
* [expert] DecelerationDamping: La valeur d’amortissement pour le calcul du facteur d’influence de la dérive au moment de dériver, c’est à dire quand on passe en dessous d’un certain seuil de vitesse d’avancement du bateau (plus cette valeur est grande, plus le bateau atteint sa vitesse de dérive cible rapidement).
* [expert] SpeedKnotsThreshold: Le seuil de vitesse en deçà duquel le bateau part à la dérive.
* MaxSpeedKnots: La valeur maximale de vitesse de dérive.
* [expert] WindSpeedReferenceKnots: La valeur de vitesse de vent référence pour mettre à l’échelle la vitesse de dérive (le TWS influe sur la vitesse de dérive).


### Données de vol (utilisées par le client)

    [XmlElement("fly")] public FlySetup Fly = new FlySetup();

* [expert] SpeedKnotsThreshold: La vitesse d’avancement seuil au delà de laquelle le bateau se met à voler (utilisée pour des bateaux tels que le Nacra 17 et le Formula 18).
* Note: si cette valeur est à 0, le bateau ne vole pas.
* [expert] HeightMeters: La hauteur à laquelle le bateau vole quand il vole.
* [expert] AccelerationDamping: La valeur d’amortissement pour l’accélération ET la décélération (plus elle est grande plus le bateau décolle/atterrit vite).



### Données d’améliorations du bateau (dépréciées, précédemment utilisées par le moteur, serveur/client)

    [XmlElement("upgrade")] public UpgradeSetup Upgrade = new UpgradeSetup();

* _BoatAdvSpeedUpwindModStep_: La valeur en pourcentage de vitesse au près ajoutée à chaque pas d’amélioration.
* _BoatAdvSpeedDownwindModStep_: La valeur en pourcentage de vitesse au portant ajoutée à chaque pas d’amélioration.
* _BoatRotModStep_: La valeur en pourcentage de vitesse de rotation ajoutée à chaque pas d’amélioration.
* _BoatSailSwitchTimeModStep_: La valeur de temps en secondes retirée au temps total de changement de voile du bateau à chaque pas d’amélioration.

Note: si ce temps est inférieur ou égal à 0, la voile change instantanément.

# Notes générales
* Les classes de configuration du client et du serveur sont identiques.
* Les données d’amélioration du bateau ne sont plus utilisées car on ne peut plus améliorer ses bateaux dans le jeu. Elle pourraient être retirées.
* Le TWA et le cap sont exprimés en degrés dans l’intervalle [-180, 180]
* Le TWD est exprimé en degrés.
* Les vitesses (avancement, dérive, TWS) sont exprimées en noeuds.
* Les données flagués [expert] sont cachées par défaut dans le panneau d’administration des bateaux sur la plateforme GameSparks.

# Démo
[Lien vers la démo](http://pierrerandria.com/BoatTest/)


# Aperçu du panneau d’administration des bateaux sur la plateforme GameSparks
## Exemple du Nacra 17:
![](https://user-images.githubusercontent.com/18393283/59673763-27449600-91c2-11e9-962a-62ca89e1bfeb.png)
![](https://user-images.githubusercontent.com/18393283/59673764-27dd2c80-91c2-11e9-9deb-d9580834b56f.png)
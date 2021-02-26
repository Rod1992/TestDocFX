# Description / commentaires des variables utilisées par le générateur de vent

_Note: je ne sais pas ce qui est implémenté exactement dans le code. Je commente les variables telles que je les avais conçues._

Comme décrit par ailleurs, le principe général est de fonctionner par des superpositions de signaux sinusoïdaux qui modifient un vent moyen général sur le plan d’eau.  
Il y a un vent moyen de base (force et direction) sur lequel est aligné le parcours, ce vent moyen peut osciller ou non et est identique sur tout le plan d’eau.  
Par dessus se placent des sources spatiales de vent (des superpositions de sinusoïdes également) qui modifient localement le vent moyen.  
D'une manière générale, on balance de l’aléatoire sur l’ensemble de ce modèle, donc lorsqu'on teste, il ne faut pas se contenter d’un seul essai pour voir le résultat.  

## Setup général
Au niveau de l’admin des challenges:

![](https://user-images.githubusercontent.com/18393283/59675698-172eb580-91c6-11e9-8763-e665cd51e245.png)

**Direction / Speed**: valeur du vent initial, avant application du modèle et des corrections. Ca correspondrait au vent que nous donnerait la NOAA ou darksky à l’endroit où se trouve le plan d’eau.  
**Variability**:  C'est un pourcentage pour donner un peu d’aléatoire à cette valeur initiale.

## Modèle d'oscillations

![](https://user-images.githubusercontent.com/18393283/59675699-172eb580-91c6-11e9-9af5-4f8563607ae0.png)

**twd_offset**: offset général du vent moyen en direction. Par exemple si on veut un vent toujours à gauche, on mettra une valeur négative (en degrés). Pas forcément nécessaire, car très prévisible du coup.

**mean_wind_osci**: true/false. Décide si on applique ou non des oscillations sur le vent de base.

**wind_amplitude_vs_tws**: important! Donne l’ordre de grandeur des variations de vent en force (dTws, en noeuds, échelle de gauche) et en direction (dTwd en degrés, échelle de droite) en fonction de la force du vent. L’idée est que dans le petit temps, le vent peut changer beaucoup en direction, mais moins en force. Alors que dans la brise, il change peu en direction mais plus en force.

![](https://user-images.githubusercontent.com/18393283/59675702-172eb580-91c6-11e9-9810-43878d37261b.png)

**source**: [ -750, -500, -250, 0, 250, 500, 750] position en latéral des sources de vent par rapport au milieu du plan d’eau. Ici 7 sources espacées de 250 m chacune. Le vent est interpolé linéairement entre deux sources.

**sinusoid mean / local**: description de chaque série de sinusoïdes. **Mean **concerne la variation du vent principal (le vent de base, qui va osciller si le **mean_wind_osci** est true). Et local concerne les sources locales / spatiales de vent.
Chaque bloc est un tableau de sinusoïdes, qui sont combinées entre elles pour calculer le vent final. En ce moment, il y a 4 sinusoïdes qui sont combinées entre elle pour calculer le vent final.

![](https://user-images.githubusercontent.com/18393283/59675703-172eb580-91c6-11e9-9bd2-f2efe7832218.png)

Chaque sinusoïde est décrite par une période, une amplitude et une longueur d’onde (pour défiler sur le plan d’eau). Les coefficients servent à calculer (aléatoirement) ces valeurs pour chaque sinusoïde.

**periodMin / periodMax**: le range de la période en **minute** de la sinusoïde. Une chiffre aléatoire est pris dans ce range.  
**twsCorr**: amplitude de la sinusoïde. C’est un pourcentage de l’amplitude max calculée en fonction du TWS moyen (donné par le tableau **wind_amplitude_vs_tws**). Par exemple si le vent moyen est de 15 nds et twsCorr est 0.36 pour une des sinusoides, l’amplitude de cette sinusoïde est de 0.36 x 6 = 2.16 kts (le 6 étant interpolé du tableau **wind_amplitude_vs_tws**)  
**twdCorr**: idem mais sur la direction. Pourcentage de la valeur de base de variation de direction donnée par le tableau wind_amplitude_vs_tws.  
**wlCorr**: correction sur la longueur d’onde. Le vent se déroule sur le plan d’eau comme un tapis roulant (qui va à la vitesse du vent). Ce facteur permet de mettre un peu d’aléatoire sur la vitesse de défilement du vent sur le plan d’eau. Plus c’est grand plus ça sera aléatoire. En pratique, cette valeur ne sert pas à grand chose.

Donc si on regarde le réglage actuel, les périodes les plus longues sont pour les amplitudes les plus grandes (pour les “grandes” bascules”) et les petites oscillations apparaissent à plus haute fréquence. Il faut faire attention que la période soit cohérente avec la durée de la régate.

**Autre**:
> tws_ratio: 1  
> twd_ratio: 1  
> period_ratio: 1  

Je ne sais pas si ces variables sont utilisées dans le code. En tous cas leur but est de multiplier globalement les variations de vent en force, direction et les périodes de sinusoïdes par un même ratio global. Si les autres variables sont bien réglées, ces trois variables n’ont pas forcément à être utilisées.

## Tuning

Pour ajuster le modèle:
* La force de vent globale se fait au niveau de l’admin des challenges (facilement reliable à une valeur retournée par DarkSky ou NOAA par exemple).
* La direction globale n’influera qu’uniquement sur l’orientation du vent sur le plan d’eau (car le parcours s’aligne avec cette direction). Mais cette variable a du sens maintenant qu’il y a plusieurs plans d’eau.
* Les variations globales en fonction du vent (plus de variations en force et/ou en direction de maniere générale) se feront le plus simplement en touchant les courbes **wind_amplitude_vs_tws**
* Pour le fine tuning d’oscillations locales, il faudra le faire sinusoide par sinusoide en changeant les ranges des périodes et les variations d’amplitudes correspondantes (**periodMin**, **periodMax**, **twsCorr**, **twdCorr**)


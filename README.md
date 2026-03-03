#  Stratégie de Pair Trading Arbitrage : BTC & ETH 

Ce dépôt contient une stratégie de **Pair Trading** (arbitrage statistique) appliquée aux deux crypto-actifs les plus liquides du marché : le Bitcoin (BTC) et l'Ethereum (ETH). La stratégie utilise le Deep Learning pour optimiser les points d'entrée et de sortie.
J'ai fait les tests statistiques au préalables (Test de Johansen, ADF (Augmented Dickey-Fuller)), les relations de cointegrations de ces 2 sont bruités par l'extreme volatilité mais on a quand même des P-values acceptables.
J'ai insisté à travaillé sur une strategie de retour à la moyenne avec cette 2 actifs car j'ai développé une approche propre à moi qui est de predire le spread entre entre ces 2 actifs avec un modèles de deep learning ,
qui est le modèle LSTM reconnue pour sa mémoire.Pour la prediction de ce spread on est dans une approche en log Spread = log(ETH) - Beta * log(BTC) pour permettre au modèle de mieux capter les dynamiques. j'ai utilisé le VIX pour la gestion du risque au detriement de l'HMM (qui preconise un trade que quand on a stationnarité) qui bloque toute activité ou ferme les positions si le marché devient trop instable
(volatilité 5% au-dessus de sa moyenne).Le spread est tradé uniquement lorsque la Volatilité Fixe est inférieure à sa moyenne long terme, ou lorsque le signal est très fort.

Seuil Dynamique :  Volatilité Fixe est utilisé aussi pour ajuster le seuil d'entrée du LSTM, rendant la stratégie plus prudente lorsque l'incertitude monte.
Le LSTM est entrainé avec des features 

un Stop-Loss statistique coupe la position si le spread évolue anormalement contre nous (2.5 écarts-types). Enfin, un Time-Stop limite la durée de chaque trade à 6 jours (36 bougies de 4h) pour éviter l'immobilisation du capital dans des positions stagnantes.

##  Concept de la Stratégie

La stratégie repose sur l'exploitation des inefficacités temporaires de corrélation entre le BTC et l'ETH. Au lieu d'une approche de retour à la moyenne classique, ce projet utilise un moteur de décision hybride.

### 1. Le Moteur de Décision (LSTM & Z-Score)
* **Modèle LSTM (Deep Learning) :** Un réseau de neurones à mémoire de forme long terme (LSTM) est utilisé pour prédire la valeur future du spread entre BTC et ETH.
* **Arbitrage de Z-Score :** On calcule un Z-Score qui mesure l'écart entre la prédiction du modèle LSTM et le prix réel du marché.
    * **Entrée :** Si l'écart est significatif (Z-Score > 1.0), une opportunité d'arbitrage est détectée.
    * **Sortie :** On dénoue la position dès que l'écart se résorbe (Z-Score < 0.2).

### 2. Gestion des Risques & Sécurité
Pour protéger le capital, trois verrous de sécurité sont implémentés :
* **Filtre de Volatilité (VIX Fix) :** La stratégie s'arrête ou coupe les positions si la volatilité du marché dépasse de 5% sa moyenne historique.
* **Stop-Loss Statistique :** Fermeture automatique de la position si le spread s'écarte de plus de 2.5 écarts-types contre nous.
* **Time-Stop :** Une limite temporelle de **6 jours** (36 bougies de 4h) est imposée pour éviter d'être piégé dans un trade qui ne converge pas.

##  Stack Technique
* **Langage :** Python 3.x
* **Data :** API Binance via CCXT (Données OHLCV 4h).
* **Analyse :** Pandas, NumPy, Scipy (Stats).
* **Machine Learning :** TensorFlow/Keras (Modèle LSTM).
* **Visualisation :** Matplotlib, Seaborn.

##  Structure du Projet
* `Pair_Trading.ipynb` : Le notebook principal contenant le téléchargement des données, l'entraînement du modèle, le backtest et la visualisation des résultats.


##  Résultats & Visualisation
Le notebook génère des graphiques de performance incluant :
- L'évolution de l'Equity Curve (Rendements cumulés).
- La distribution des Z-Scores.
- L'analyse des rendements en fonction de la volatilité.
- la repartition des positions etc

---
**Note :** koffidayan8@gmail.com pour mieux expliqué ma stratégie si vous avez des questions

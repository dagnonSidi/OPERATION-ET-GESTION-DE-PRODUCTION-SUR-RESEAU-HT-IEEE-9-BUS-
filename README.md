# OPÉRATION ET GESTION DE PRODUCTION (Dispatch) SUR RÉSEAU HT IEEE-9 BUS

Ce projet vise à réaliser des simulations de load flow et à optimiser le coût total de production d’un réseau électrique en jouant sur des paramètres de dispatch (puissances actives/réactives) et de tensions. Les études combinent des simulations de référence (cas de base, analyse de congestion) et des routines d’optimisation pour comparer méthodes numériques et approches exhaustives. Il contient deux Bureaux d’Études (BE).

**Bureau d’Études 1 (Lab 2)**  
Objectif principal : établir et valider le modèle de load flow du réseau (topologie, paramètres des lignes, charges et générateurs) et produire les cas de base.  
Travaux : calculs de tensions et flux de puissance, vérification des limites (courant/puissance/tension), étude d’impact de scénarios de charge et identification des lignes critiques.

**Bureau d’Études 2 (Lab 3)**  
Objectif principal : optimiser le dispatch des unités de production pour minimiser le coût total de production sous contraintes (puissance apparente des lignes, limites de génération et enveloppe de tension).  
Travaux : formulation des fonctions objectives (coût, pertes, critères de fiabilité), essais comparatifs (recherche exhaustive, méthode du gradient, optimiseur `fmincon`), et analyses de scénarios (congestion, contingences, délestage).

---

# CONTENU DU RÉPOSITORY

L'objectif principal de ce dépôt est de **partager les résultats** que j'ai obtenus lors du projet.  
Le dépôt contient : ce README détaillé avec les résultats présentés, ainsi que le rapport complet du projet en PDF.  
Certains scripts MATLAB pourront être partagés ; cependant, les fichiers de code complets fournis ne sont **pas** inclus dans ce répertoire.

---

# Description du réseau (schéma représentatif)
Le réseau étudié est un **réseau de type pédagogique IEEE modifié**, comportant :
- 3 générateurs (bus 1, 2, 3) et des charges réparties principalement sur les nœuds 5, 6 et 8 ; demande totale : **475 MW** et **175 MVAr**.  
- <img width="403" height="225" alt="image" src="https://github.com/user-attachments/assets/d08c0b16-4a7d-4438-8ee8-9f791f077c76" />

- Caractéristiques principales des générateurs :
  - **G1 (bus 1)** : prix marginal 25 €/MWh — Production max **300 MVA**.  
  - **G2 (bus 2)** : prix 75 €/MWh — Production max **300 MVA**.  
  - **G3 (bus 3)** : prix 50 €/MWh — Production max **200 MVA**.  
- Limites réglementaires de tension : **±10 %** (EN-EC 50-160).

---

# **Bureau d’Études 1 (Lab 2)**

L'objectif de ce Lab 2 est d’étudier et d’optimiser la performance opérationnelle du réseau en ajustant les tensions et le dispatch (puissances actives/réactives) afin de minimiser le coût, les pertes, tout en respectant les contraintes (Sn des lignes, limites de tension et de génération), et enfin de maximiser le coefficient de lestage en cas de congestion de lignes.  
Il est divisé en trois parties :

- **PART I** — minimisation des coûts (ordre de mérite, scénarios).  
- **PART II** — minimisation des pertes (tâtonnement, gradient).  
- **PART III** — maximisation du coefficient de lestage (gestion du délestage, étude de contingence).

### PART I — minimisation des coûts (ordre de mérite, scénarios)
Dans cette partie, le coût total de production est minimisé en utilisant le **merit order** sur le réseau. En résumé, on produit d’abord avec les centrales les moins chères.  
L’ordre d’appel des centrales est : **G1 (25 €/MWh)** → **G3 (50 €/MWh)** → **G2 (75 €/MWh)**.  
Cela permet d’obtenir un coût total de production de l’ordre de **16 800 €**.

- <img width="526" height="246" alt="image" src="https://github.com/user-attachments/assets/63991e83-8a31-4c80-bce6-3377df4c917d" />

**D'autres scénarios avec des congestions sont présentés en détail dans le rapport PDF.**

---

### PART II — minimisation des pertes (tâtonnement, méthode des gradients)
Pour cette partie, on suppose que chaque centrale produit au même prix :
- **G1 (bus 1)** : prix marginal 50 €/MWh — Production max **300 MVA**.  
- **G2 (bus 2)** : prix 50 €/MWh — Production max **300 MVA**.  
- **G3 (bus 3)** : prix 50 €/MWh — Production max **200 MVA**.  

L’objectif est de **minimiser les pertes actives ($\sum_{l} R_{l} I_{l}^{2}$)** du réseau en ajustant simultanément les tensions et le dispatch des unités (puissances actives/réactives).  
Les méthodes testées incluent un **tâtonnement / balayage paramétrique** et une approche **par gradient**. En combinant le réglage des tensions et la redistribution de la production, on obtient une réduction notable des pertes par rapport à la stratégie purement économique.

**Résultat représentatif :** la meilleure solution trouvée donne des pertes ≈ **0.0891 pu** (plan exemple : PG1 ≈ 2.55 pu, PG3 ≈ 1.30 pu), soit environ **8 MW** de pertes pour **475 MW** de charge.  
Plusieurs paramétrages ont été testés, les pertes correspondantes figurent dans le tableau ci-dessous :

- <img width="256" height="332" alt="image" src="https://github.com/user-attachments/assets/0ad42e73-0506-49e1-addc-14b5351fe25f" />

---

### PART III — maximisation du coefficient de lestage (gestion du délestage, étude de contingence)
Dans cette partie, on suppose que la **ligne L6** est hors service. Par conséquent, les deux lignes restantes pour alimenter la charge 6 ne suffisent plus (**L2 = 0.4 pu** et **L5 = 2 pu** sont congestionnées).

L’objectif est de **maintenir le maximum de clients alimentés** afin de minimiser les pénalités liées à l’énergie non fournie, tout en respectant les limites de puissance apparente des lignes.

**Résultat :** en ajustant les valeurs de tension et de puissance, on a pu maintenir **85 %** des clients alimentés. Les résultats sont présentés dans l’image ci-dessous :

<img width="1086" height="461" alt="image" src="https://github.com/user-attachments/assets/ee208b06-202e-420b-b007-d97bcc0f8d7b" />

**Remarque :** plus de détails sur la méthode et les pénalités associées (pouvant atteindre **800 000 €**) sont fournis dans le rapport.

---

# **Bureau d’Études 2 (Lab 3)** — comparaison des méthodes d'optimisation : recherche exhaustive vs `fmincon` (optimisation automatique)

Dans cette partie, l’objectif est de comparer deux méthodes d'optimisation pour minimiser soit le **coût total de production**, soit les **pertes**, et d’évaluer leur efficacité.  
Les deux méthodes étudiées sont : **recherche exhaustive / balayage paramétrique** et **optimiseur `fmincon`** de MATLAB.  
Ce BE2 (Lab 3) est divisé en deux parties :

- **PART I** — Recherche exhaustive.  
- **PART II** — Optimisation avec `fmincon`.

### PART I — Recherche exhaustive pour minimiser les pertes
Dans cette partie, on définit les générateurs **G2** et **G3** comme nœuds PV et **G1** comme nœud Slack.  
La méthode exhaustive consiste à trouver les valeurs de **P2** et **P3** donnant les pertes minimales, en testant toutes les combinaisons possibles (pas de variation = 0.1).  
Une fois la matrice de pertes obtenue, on utilise la fonction `surf` de MATLAB pour visualiser les zones de pertes et identifier le minimum global.

<img width="1073" height="529" alt="image" src="https://github.com/user-attachments/assets/cde0934c-7a43-4e2b-8243-df11b7a138d2" />

Les pertes actives étant une fonction convexe, le minimum se trouve dans le creux de la surface.  
Sans contrainte de tension ni de puissance, on a obtenu une valeur minimale de **P_losses = 0.0888 pu**, correspondant à **P2 = 0.9 pu**, **P3 = 1.30 pu**.

**Remarque :** plus de détails et d’autres scénarios (avec contraintes de tension et de puissance) sont présentés dans le rapport PDF.

---

### PART II — Optimisation automatique avec `fmincon`
Dans cette partie, on utilise la fonction **`fmincon`** de MATLAB, qui permet de résoudre des problèmes d’optimisation sous contraintes sur des fonctions convexes.  
On définit la fonction à optimiser (pertes ou coût total) et on cherche les valeurs optimales du vecteur [V1, V2, V3, P2, P3].

#### Fmincon pour optimiser les pertes actives
`fmincon` permet d’obtenir les valeurs optimales du vecteur [V1, V2, V3, P2, P3] minimisant les pertes.  
Après simulation, on a obtenu sans contrainte une valeur de **P_losses = 0.0887 pu**.

<img width="308" height="333" alt="image" src="https://github.com/user-attachments/assets/1575ff3b-77c7-4955-984c-f40cba20bcde" />

#### Fmincon pour optimiser le coût
`fmincon` permet également de minimiser le **coût total** en ajustant le même vecteur de variables. La fonction objectif est définie ainsi :

<img width="1022" height="91" alt="image" src="https://github.com/user-attachments/assets/c0837e28-3c61-450b-a468-488a77d4c345" />

Après simulation, on a obtenu sans contrainte un coût minimal de **16 740 €** :

<img width="364" height="84" alt="image" src="https://github.com/user-attachments/assets/dc1bc2b9-f3f7-40f2-9393-938c2ba1f877" />

**Remarque :** plus de détails sur la méthode et les simulations avec contraintes (tension et puissance) sont présentés dans le rapport PDF.

---

# Autres Membres du projet
1. Anthony PEUJIO  
2. Mireille NGUEPTCHOUANG

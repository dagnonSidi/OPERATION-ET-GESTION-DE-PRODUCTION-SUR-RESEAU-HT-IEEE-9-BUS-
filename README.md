# OPERATION ET GESTION DE PRODUCTION (Dispatch) SUR RESEAU HT IEEE-9 BUS

Ce projet vise à réaliser des simulations de load flow et à optimiser le coût total de production d’un réseau électrique en jouant sur des paramètres de dispatch (puissances actives/réactives) et de tensions. Les études combinent des simulations de référence (cas de base, analyse de congestion) et des routines d’optimisation pour comparer méthodes numériques et approches exhaustives. Il contient deux BE (bureau d'étude) 

**Bureau d’Études 1 (Lab 2)**:
Objectif principal : établir et valider le modèle de load flow du réseau (topologie, paramètres des lignes, charges et générateurs) et produire les cas de base. 
Travaux : calculs de tensions et flux de puissance, vérification des limites (courant/puissance/voltage), étude d’impact de scénarios de charge et identification des lignes critiques.

**Bureau d’Études 2 (Lab 3)**:
Objectif principal : optimiser le dispatch des unités de production pour minimiser le coût total de production sous contraintes (puissance apparente des lignes, limites de génération et enveloppe de tension).
Travaux : formulation des fonctions objectifs (coût, pertes, critères de fiabilité), essais comparatifs (recherche exhaustive, méthode de gradient, optimiseur fmincon), et analyses de scénarios (congestion, contingences, délestage).


# CONTENU DU REPOSITOIRE

L'objectif principal de ce dépôt est de **partager les résultats** que j'ai obtenus lors du projet.
Le depot contiendra : ce Readme bien détaillé avec les résultats présentés. Il contiendra aussi le rapport détaillé du projet en pdf. 
Certains scripts matlab pourront etre partager mais cependant les fichiers de codes complet matlab ne sont pas sur ce repositoire.



---

# Description du réseau (schéma représentatif)
Le réseau étudié est un **réseau de type étude pédagogique IEEE modifié** avec :
- 3 générateurs (bus 1, 2, 3) et charges réparties principalement sur les nœuds 5, 6 et 8 ; demande totale : **475 MW** et **175 MVAr**. :contentReference[oaicite:1]{index=1}
- <img width="403" height="225" alt="image" src="https://github.com/user-attachments/assets/d08c0b16-4a7d-4438-8ee8-9f791f077c76" />

- Caractéristiques principales des générateurs :
  - **G1 (bus 1)** : prix marginal 25 €/MWh — Production max **300 MVA**.  
  - **G2 (bus 2)** : prix 75 €/MWh — Production max **300 MVA**.  
  - **G3 (bus 3)** : prix 50 €/MWh — Production max **200 MVA**.  
- Limites réglementaires de tension : **±10%** (EN-EC 50-160). :contentReference[oaicite:5]{index=5}  


# **Bureau d’Études 1 (Lab 2)**:
L'Objectif de ce Lab 2 est : étudier et optimiser la performance opérationnelle du réseau en ajustant tensions et dispatch (puissances actives/réactives) 
pour minimiser le cout, les pertes tout en respectant les contraintes (Sn des lignes, limites de tension et de génération) et enfin maximiser le coefficient de lestage en cas de congestions de lignes.
Il est divisé en 3 parties : 

  - PART I — minimisation des coûts (ordre de mérite, scénarios).
  - PART II — minimisation des pertes (tâtonnement, gradient).
  - PART III — maximisation du coefficient de lestage (gestion du délestage, étude de contingence). 
  ### PART I — minimisation des coûts (ordre de mérite, scénarios).
Dans cette partie on minimise le coût total de production  est minimisé en utilisant le merit order sur le réseau. En gros on produit avec les centrales les moins chers d'abord. En utilisant cette méthode l'ordre d'appel
des centrales est : G1 (25€/MWh) --> G3 ( 50 €/MWh) --> G2 (75€/MWh). Cela permet d'avoir un cout totale de production de l'ordre 16800 €

- <img width="526" height="246" alt="image" src="https://github.com/user-attachments/assets/63991e83-8a31-4c80-bce6-3377df4c917d" />

** D'autres cas de scénarios avec des congestions sont présentés en details dans le rapport pdf **

  ### PART II — minimisation des pertes (tâtonnement, méthode des gradients).
Pour cette partie on a supposé que chaque centrale produsait au meme prix :
  - **G1 (bus 1)** : prix marginal 50 €/MWh — Production max **300 MVA**.  
  - **G2 (bus 2)** : prix 50 €/MWh — Production max **300 MVA**.  
  - **G3 (bus 3)** : prix 50 €/MWh — Production max **200 MVA**. 

L’objectif est de **minimiser les pertes actives ($\sum_{l} R_{l} I_{l}^{2}$)** du réseau en ajustant simultanément les tensions et le dispatch des unités (puissances actives/réactives).  
Les méthodes testées incluent un **tâtonnement / balayage paramétrique** et une approche **par gradient** (optimisation locale). En combinant réglage de tensions et redistribution de la production, on obtient une réduction notable des pertes par rapport à la stratégie purement économique.

**Résultat représentatif :** la meilleure solution trouvée donne des pertes ≈ **0.0891 pu** (plan exemple : PG1 ≈ 2.55 pu, PG3 ≈ 1.30 pu).  Ce qui équivaut à 8 MW de pertes pour 475MW de charges.
On a fait plusieurs paramétrage, et les pertes correspondant à chacun est dans le tableau suivant:

- <img width="256" height="332" alt="image" src="https://github.com/user-attachments/assets/0ad42e73-0506-49e1-addc-14b5351fe25f" />



  ###  PART III —maximisation du coefficient de lestage (gestion du délestage, étude de contingence).
Dans cette partie on supposé que la ** ligne L6** est hors service. Par conséquent deux lignes restantes pour alimenter la charge 6 ne suffisent plus. **L2 = 0.4 pu** et **L5 = 2 pu* sont congstiées.

L'objectif dans ce contexte est maintenir le plus possible de client (lestage) sous peine de recevoir des pénalités pour chaque client non alimenté: maximiser le coefficient de lestage, tout en respectant les limites de puissance apparente sur certaines lignes.


**Résultat**: En jouant sur les valeurs de tensions et de puissant on pu maintenir 85% des clients. Les résulats sont présenté sur l'image ci-dessus.

<img width="1086" height="461" alt="image" src="https://github.com/user-attachments/assets/ee208b06-202e-420b-b007-d97bcc0f8d7b" />


**Remarque**: plus de détails sur la méthode et aussi sur les pénalités à payer pouvant aller jusqu'à 800 000 euros sont présentés dans le rapport


  
# **Bureau d’Études 2 (Lab 3)**:   
- **Lab n°3** — comparaison des méthodes d'optimisation : recherche exhaustive vs `fmincon` (optimisation automatique). :contentReference[oaicite:12]{index=12}

Ces parties sont reprises dans le dossier `results/` avec un résumé des principaux résultats et les figures correspondantes.



---

### Lab n°3 — Optimisation automatique vs recherche exhaustive
- Méthodes : recherche exhaustive (balayage), `fmincon` (optimiseur Matlab).  
- Résultats clés : `fmincon` trouve des solutions plus rapidement et avec des pertes plus faibles que la recherche exhaustive (exemples chiffrés ci-dessous). 

#### Comparaison (extrait)
![Comparaison pertes — méthodes](sandbox:/mnt/data/results_comparison.png)

- Valeurs représentatives (extrait du rapport) :
  - **Exhaustive (avec contraintes)** : `P_losses = 0.1020 pu` (temps de calcul plus long). :contentReference[oaicite:18]{index=18}  
  - **Fmincon (avec contraintes)** : `P_losses ≈ 0.08895 pu` (plus rapide et meilleur résultat). :contentReference[oaicite:19]{index=19}  
  - **Gradient / solution manuelle** : `P_losses ≈ 0.0891 pu`. :contentReference[oaicite:20]{index=20}

---

## Fichiers que j'ai ajoutés pour le dépôt (prêt à push)
- `network_schematic.png` — schéma illustratif (à placer dans `docs/` ou `assets/`).  
- `results_comparison.png` — figure comparative des pertes par méthode.  
- `key_results_summary.csv` — résumé numérique des valeurs clefs.  
(Téléchargement ci-dessous.)

---

## Commandes rapides pour ajouter ces fichiers et committer
```bash
# depuis le dossier du projet local
mkdir -p results assets report code
# placer report/report.pdf (ton PDF) dans report/
# ajouter les images téléchargées dans assets/ ou results/
git add README.md NOTICE LICENSE report/ assets/ results/ code/
git commit -m "Ajout README, NOTICE, résultats et images illustratives"
git push origin main

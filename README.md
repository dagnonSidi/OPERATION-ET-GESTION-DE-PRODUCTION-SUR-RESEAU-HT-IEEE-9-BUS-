# OPERATION ET GESTION DE PRODUCTION SUR RESEAU HT IEEE-9 BUS

Ce projet vise à réaliser des simulations de load flow et à optimiser le coût total de production d’un réseau électrique en jouant sur des paramètres de dispatch (puissances actives/réactives) et de tensions. Les études combinent des simulations de référence (cas de base, analyse de congestion) et des routines d’optimisation pour comparer méthodes numériques et approches exhaustives. Il contient deux BE (bureau d'étude) 

Bureau d’Études (Lab 1):
Objectif principal : établir et valider le modèle de load flow du réseau (topologie, paramètres des lignes, charges et générateurs) et produire les cas de base. 
Travaux : calculs de tensions et flux de puissance, vérification des limites (courant/puissance/voltage), étude d’impact de scénarios de charge et identification des lignes critiques.

Bureau d’Études (Lab 2):
Objectif principal : optimiser le dispatch des unités de production pour minimiser le coût total de production sous contraintes (puissance apparente des lignes, limites de génération et enveloppe de tension).
Travaux : formulation des fonctions objectifs (coût, pertes, critères de fiabilité), essais comparatifs (recherche exhaustive, méthode de gradient, optimiseur fmincon), et analyses de scénarios (congestion, contingences, délestage).


# Projet — Optimisation de dispatch et résultats (Load Flow)

## Objectif
L'objectif de ce dépôt est de **partager uniquement les résultats** que j'ai obtenus lors du projet (simulations et optimisations).  
Ce projet porte sur l'optimisation du dispatch d'un réseau électrique en jouant sur les paramètres de tension, les puissances et les fonctions objectif. Seuls les **résultats, figures et scripts que j'ai écrits** seront publiés ici ; le code original fourni par l'enseignant **n'est pas inclus** (voir section Attribution).

---

## Remarque importante — attribution
Les fichiers fournis par **[NOM_PROFESSEUR]** (ci-après « code d'origine ») **ne sont pas inclus** dans ce dépôt public.  
Ce dépôt contient uniquement :
- mon rapport : `report/report.pdf`
- mes scripts et analyses (écrits par **[TON NOM]**) : `code/`
- résultats et figures : `results/`  

La licence indiquée dans `LICENSE` s'applique uniquement aux fichiers créés par **[TON NOM]**. Pour obtenir le code d'origine, contactez directement **[NOM_PROFESSEUR]**.  
(Extrait et synthèse des objectifs et méthodes du rapport). :contentReference[oaicite:0]{index=0}

---

## Description du réseau (schéma représentatif)
Le réseau étudié est un **réseau de type étude pédagogique** avec :
- 3 générateurs (bus 1, 2, 3) et charges réparties principalement sur les nœuds 5, 6 et 8 ; demande totale : **475 MW** et **175 MVAr**. :contentReference[oaicite:1]{index=1}  
- Caractéristiques principales des générateurs :
  - **G1 (bus 1)** : prix marginal 25 €/MWh — max **3 pu**. :contentReference[oaicite:2]{index=2}  
  - **G2 (bus 2)** : prix 75 €/MWh — max **3 pu**. :contentReference[oaicite:3]{index=3}  
  - **G3 (bus 3)** : prix 50 €/MWh — max **2 pu**. :contentReference[oaicite:4]{index=4}  
- Limites réglementaires de tension : **±10%** (EN-EC 50-160). :contentReference[oaicite:5]{index=5}  
- Puissances apparentes admissibles (Sn_max) : par défaut 9.9 pu sur les lignes ; certains scénarios testent des réductions (ex. L4=0.5, L5=0.9, L7=0.5 pu). :contentReference[oaicite:6]{index=6}

> Schéma représentatif (illustration) — voir image ci-dessous.

![Schéma réseau (illustration)](sandbox:/mnt/data/network_schematic.png)

*(Schéma illustratif créé pour le README — se référer au rapport pour la topologie exacte).* :contentReference[oaicite:7]{index=7}

---

## Contenu du dépôt
- `report/report.pdf` — ton rapport complet (fournit les scénarios, méthodes et figures originales). :contentReference[oaicite:8]{index=8}  
- `code/` — scripts **que j'ai écrits** (wrappers, post-traitement, génération des figures).  
- `results/` — figures, captures et fichiers `.mat` / `.csv` contenant les sorties des simulations et des optimisations (extraits ci-dessous).  
- `NOTICE` — clause d’attribution (exclusion du code d’origine).  
- `LICENSE` — licence s’appliquant uniquement aux fichiers créés par moi.

---

## Parties importantes identifiées dans le rapport (TOC synthétique)
Le rapport contient les parties suivantes (identifiées et reprises ci-dessous) :  
- **Lab n°2**  
  - PART I — minimisation des coûts (ordre de mérite, scénarios). :contentReference[oaicite:9]{index=9}  
  - PART II — minimisation des pertes (tâtonnement, gradient). :contentReference[oaicite:10]{index=10}  
  - PART III — maximisation du coefficient de lestage (gestion du délestage, étude de contingence). :contentReference[oaicite:11]{index=11}  
- **Lab n°3** — comparaison des méthodes d'optimisation : recherche exhaustive vs `fmincon` (optimisation automatique). :contentReference[oaicite:12]{index=12}

Ces parties sont reprises dans le dossier `results/` avec un résumé des principaux résultats et les figures correspondantes.

---

## Résultats clés — résumé par partie (extraits & figures)

### Lab n°2 — PART I : Minimisation des coûts
- Méthode : ordre de mérite pour l'appel des unités et simulations de load flow.  
- Résultat représentatif : en scénario sans congestion on obtient un dispatch économiquement efficace (coût dans l'ordre de **~16 k€** selon les scénarios étudiés).   
- Figure associée : voir `results/cost_dispatch_example.png` (si fournie).  
- Remarque : la congestion (réduction de Sn_max sur certaines lignes) augmente le coût — ex. coûts plus élevés avec Sn_max réduits sur L4/L5/L7. :contentReference[oaicite:14]{index=14}

---

### Lab n°2 — PART II : Minimisation des pertes
- Méthodes testées : tâtonnement (balayage), méthode des gradients.  
- Résultats : réduction importante des pertes par réglage des tensions et dispatch — exemple final obtenu **P_losses ≈ 0.0891 pu** pour un plan PG1 = 2.55 pu, PG3 = 1.3 pu (après ajustements de tensions).   
- Figure associée : matrice des pertes en fonction de P2/P3 et carte 3D (voir `results/`).

---

### Lab n°2 — PART III : Maximisation du coefficient de lestage (gestion du délestage)
- Contexte : mise hors service de la ligne L6 (contingence).  
- Objectif : maximiser le % de clients alimentés (coefficient de lestage).  
- Résultat : on atteint jusqu'à **0.85–0.91** du coefficient de lestage selon les scénarios, en acceptant un léger surcoût mais en réduisant fortement les pénalités liées aux coupures. Exemple numérique : réduction des pénalités de ~875 000 € → 375 000 € en passant de 0.65 → 0.85. 

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

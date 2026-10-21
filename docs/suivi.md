---
title: Suivi du projet
---

<style>
    @media screen and (min-width: 76em) {
        .md-sidebar--primary {
            display: none !important;
        }
    }
</style>

# Suivi de projet

> :bulb: Cette page documente l'évolution du projet dans le temps.
> Elle sert à rendre visibles les décisions, ajustements et apprentissages.
> Les entrées sont bi-hebdomadaires.
> N'oubliez pas d'effacer ou de mettre en commentaires les notes (`>`) avant la remise finale.

---

## Semaine du 22 septembre au 3 octobre 2026

### Objectifs de la période
- Concevoir le pipeline de tri de pertinence des articles de veille

### Travail réalisé

!!! abstract "Avancement"
    - [x] Collecte des articles (Inoreader / flux RSS)
    - [x] Filtrage rapide par embeddings et similarité
    - [x] Reranking des articles retenus
    - [x] Évaluation de pertinence par un modèle de langue (juge), en local via Ollama
    - [x] Classement et sélection du top N

### Décisions et ajustements

!!! info "Décisions"
    - J'ai découpé le traitement en plusieurs étapes, de la plus rapide à la plus précise, pour garder un bon équilibre entre qualité, vitesse et coût

### Difficultés rencontrées

> À compléter si un obstacle a eu un impact réel sur l'avancement.

---

## Semaine du 6 au 17 octobre 2026

### Objectifs de la période
- Concevoir l'interface de la plateforme

### Travail réalisé

!!! abstract "Avancement"
    - [x] Rencontres pour cadrer l'interface et décider à quoi elle allait ressembler
    - [x] Maquettes Figma des écrans principaux
    - [x] Page d'accueil (choix du service)
    - [x] Écran du service de veille (liste d'articles, score de pertinence, filtres)
    - [x] Début du développement de l'interface

**Maquette :** [Interface globale (Figma)](https://www.figma.com/design/g2L7RuF97pobUbv4sjRH6m/MESS?node-id=1-4222)

### Décisions et ajustements

!!! info "Décisions"
    - Lors des rencontres, on a décidé de la structure et de l'allure de l'interface
    - J'ai pensé l'interface pour quelqu'un qui n'est pas technique

### Difficultés rencontrées

> À compléter si un obstacle a eu un impact réel sur l'avancement.

---

## Semaine du 20 octobre 2026

### Objectifs de la période
- Finir l'interface de veille et démarrer la partie résumé

### Travail réalisé

!!! abstract "Avancement"
    - [x] Interface de VeilleIA terminée et fonctionnelle (affichage des articles, mise en avant des plus pertinents, filtres)
    - [ ] Nouveau prototype de l'interface de résumé (en cours)


### Décisions et ajustements

!!! info "Décisions"
    - Changement de priorité : au lieu de commencer la base de données des collections, on vise d'abord une démonstration rapide
    - Prochain objectif concret : trouver une façon de résumer plusieurs documents internes en même temps

### Difficultés rencontrées

> À compléter si un obstacle a eu un impact réel sur l'avancement.

---

<!--
    GABARIT pour les prochaines entrées — copier/coller et compléter :

## Semaine du <dates>

### Objectifs de la période
- ...

### Travail réalisé
!!! abstract "Avancement"
    - [ ] ...

### Décisions et ajustements
### Difficultés rencontrées
-->

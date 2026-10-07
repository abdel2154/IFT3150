---
title: Vue d'ensemble du projet
---

<style>
    @media screen and (min-width: 76em) {
        .md-sidebar--primary {
            display: none !important;
        }
    }
</style>

# Vue d'ensemble du projet

!!! info "Informations générales"
    **Session**: Automne 2026  
    **Auteur(s)**: Abdel Messaad (20287581)  
    **Thème(s)**: Intelligence artificielle appliquée : veille informationnelle et base documentaire assistées par IA (plateforme interne de services)  
    **Superviseur(s)**: <!-- PLACEHOLDER : nom du superviseur (affiliation) à compléter -->  
    **Collaborateur(s):** Ministère de l'Emploi et de la Solidarité sociale (MESS), Gouvernement du Québec  

## Description du projet

### Contexte

Le projet s'inscrit dans un mandat réel au sein du Ministère de l'Emploi et de la Solidarité sociale (MESS) du Québec, un ministère dont les activités touchent notamment l'emploi, le marché du travail et la solidarité sociale.

Dans ce type d'organisation, les équipes doivent composer avec un volume croissant d'information et de documents : actualités, publications d'organismes, rapports, études, documents administratifs. Une partie du travail de veille, de lecture et d'analyse documentaire demeure largement manuelle et chronophage.

L'intelligence artificielle (recherche sémantique, *embeddings*, modèles de langue) ouvre des pistes pour assister ces tâches. Mais dans un contexte gouvernemental, ces pistes doivent impérativement tenir compte de la confidentialité, de la gouvernance des données et de la sécurité (voir la section [Contraintes et confidentialité](#contraintes-et-confidentialite)).

### Problématique

Les personnes qui font de la veille et de l'analyse documentaire au ministère font face à une surcharge informationnelle :

- une grande quantité d'articles, de sources et de documents à suivre ou à traiter ;
- un effort important pour distinguer ce qui est réellement pertinent pour un besoin donné ;
- un traitement manuel (lecture, tri, recoupement) qui ne passe pas à l'échelle ;
- des connaissances utiles dispersées dans de nombreux documents difficiles à exploiter ensemble.

Les approches actuelles (tri manuel, recherche par mots-clés) montrent leurs limites : elles sont lentes et peinent à saisir la pertinence sémantique d'un contenu par rapport à un besoin.

### Proposition et objectifs

La piste explorée est une plateforme interne regroupant plusieurs services d'IA, accessible depuis une page d'accueil unique, pensée pour des utilisateurs non techniques.

Deux services forment le cœur du projet pour cette session :

1. Veille assistée par IA : collecter automatiquement des articles et retourner uniquement les plus pertinents grâce à un pipeline combinant *embeddings*, *reranking* et un modèle de langue comme juge.
2. Base documentaire : un corpus que l'utilisateur enrichit lui-même (articles jugés pertinents et ses propres PDF), sur lequel les modèles de langue s'appuient lors de leurs analyses (approche *RAG*, voir plus bas).

!!! note "Extensions envisagées (hors cœur de session)"
    - Résumé et synthèse de documents sélectionnés (points clés, chiffres, recommandations).
    - Classification documentaire automatique selon un plan de classification.

    Ces services sont envisagés comme évolutions futures de la plateforme et ne constituent pas le livrable principal de la session.

Objectifs préliminaires (à raffiner avec le superviseur) :

- [ ] Cadrer le besoin de veille et le périmètre des sources
- [ ] Concevoir l'interface de la plateforme (page d'accueil et service de veille)
- [ ] Prototyper le pipeline de pertinence de la veille (preuve de concept)
- [ ] Prototyper la base documentaire (ingestion de PDF et d'articles, puis recherche)
- [ ] Définir une démarche d'évaluation de la pertinence

## Récits d'utilisation

> Exemples illustrant *qui* veut faire *quoi*, avec *quelles données*. Ils servent à préciser le besoin ; certains restent à valider.

!!! abstract "Récit 1 : Veille avec tri par IA"
    En tant que conseiller en veille, je veux que le système récupère automatiquement les articles de mes sources et me retourne uniquement les plus pertinents pour ma thématique, afin de ne pas lire des centaines d'articles.
    Données : articles publics (presse, flux RSS) collectés via Inoreader.

!!! abstract "Récit 2 : Base documentaire personnelle"
    En tant qu'analyste, je veux sauvegarder les articles pertinents trouvés par la veille et téléverser mes propres PDF dans une base documentaire, afin que les modèles de langue s'appuient sur ce corpus lors de leurs analyses (questions, recoupements).
    Données : articles retenus et documents PDF fournis par l'utilisateur.

!!! abstract "Récit 3 : Accès par une plateforme unique"
    En tant qu'utilisateur interne non technique, je veux une page d'accueil où choisir le service et naviguer simplement, afin d'utiliser l'IA sans en comprendre les rouages.
    Données : sans objet (interface).

!!! abstract "Récits envisagés (extensions)"
    - En tant qu'analyste en politiques, je veux un résumé structuré d'un long rapport (points clés, chiffres, recommandations), afin de préparer une note de breffage plus vite. *(Extension : résumé.)*
    - En tant que technicien en gestion documentaire, je veux que le système propose la catégorie d'un document selon le plan de classification, afin de réduire le classement manuel. *(Extension : classification. Démonstration envisagée sur données synthétiques.)*

## Comment fonctionne la veille (pipeline de pertinence)

L'idée est de combiner des méthodes rapides mais grossières et des méthodes plus coûteuses mais précises, du filtrage large vers le classement fin :

| Étape | Rôle | Approche envisagée |
|-------|------|--------------------|
| 1. Collecte | Récupérer les articles (titre, source, date, contenu) | API Inoreader |
| 2. Embeddings et similarité | Filtrage rapide : écarter le manifestement hors-sujet | Modèle d'*embeddings* exécuté localement |
| 3. Reranking | Reclasser finement les articles retenus selon la requête | *Cross-encoder* / *reranker* |
| 4. Modèle de langue comme juge | Évaluer la pertinence (besoin, thématique, requête, titre, contenu) et produire un score avec justification | Modèle local via Ollama |
| 5. Score et classement | Retourner le top N trié par pertinence | (sans objet) |

```text
Inoreader → Articles → Embeddings/similarité → Reranking → Modèle juge → Score → Top N pertinent
```

## Base documentaire (approche RAG)

La base documentaire est un corpus curé par l'utilisateur. Les articles jugés pertinents et les PDF téléversés y sont ingérés (extraction du texte, puis découpage en *chunks*, puis *embeddings*, puis stockage dans une base vectorielle locale). Lors d'une analyse, les passages les plus pertinents sont récupérés et fournis au modèle de langue comme contexte : c'est le principe du *RAG* (*retrieval-augmented generation*).

```text
Articles retenus ─┐
                  ├─► Ingestion (extraction → chunks → embeddings) ─► base vectorielle locale
PDF téléversés ───┘
                           Analyse / question ─► récupération (RAG) ─► modèle local (Ollama) ─► réponse ancrée
```

## Contraintes et confidentialité

!!! warning "Contexte gouvernemental : principe directeur"
    Les documents de la base documentaire peuvent être internes au ministère. Par conséquent, le traitement par l'IA se fait localement : les modèles de langue sont exécutés sur place via Ollama, et la base vectorielle est locale. Aucune donnée interne n'est envoyée à un service externe.

Points clés de l'approche :

- Modèles de langue locaux (Ollama) : les analyses s'exécutent sur l'infrastructure du ministère, sans dépendance à une API externe.
- Séparation des données : Inoreader ne manipule que des contenus publics (articles, flux RSS) ; les documents internes restent dans l'environnement local.
- Critères de décision : les choix techniques tiennent compte de la qualité, de la précision, de la latence, du coût, de la sécurité et de l'explicabilité, et pas seulement de la performance.

> À valider avec le superviseur : hébergement, politiques d'accès et niveau de sensibilité des documents admis dans la base.

### Technologies envisagées

> :bulb: À ce stade, il s'agit de pistes à expérimenter, non de choix définitifs ni d'un système déjà implémenté.

- Ollama : exécution locale des modèles de langue (modèles open-source précis à déterminer selon le matériel disponible).
- Modèles d'*embeddings* et *reranker* : à sélectionner (priorité au support du français).
- Base vectorielle locale (p. ex. ChromaDB) et orchestration (p. ex. LangChain) : à valider.
- Inoreader : collecte des sources de veille.
- Interface : maquettage exploré avec des outils comme Stitch et Figma.

### Méthodologie

- Démarche itérative, par preuves de concept (POC) : comprendre un besoin, prototyper, comparer des approches, évaluer la pertinence réelle.
- Prototypage de l'interface avant développement.
- Comparaison d'approches IA à chaque étape du pipeline, en gardant à l'esprit les critères ci-dessus.

### Validation et Évaluation

Pistes envisagées (non arrêtées) :

- scénarios d'usage représentatifs (ex. une thématique de veille réelle) ;
- retours du superviseur et d'utilisateurs internes ;
- indicateurs sur la qualité du tri de pertinence (ex. comparaison du classement du système avec un jugement humain sur un échantillon).

> À réfléchir : comment mesurer la « pertinence » d'un article ou d'un document de façon crédible et reproductible ?

## Échéancier

!!! info
    Le suivi complet est disponible dans la page [Suivi de projet](suivi.md).

<!-- PLACEHOLDER : dates et jalons à confirmer avec le plan de cours (session Automne 2026) -->

| Activités                      | Début       | Fin         | Livrable                            | Statut       |
|--------------------------------|-------------|-------------|-------------------------------------|--------------|
| Ouverture de projet            | Sept. 2026  | Sept. 2026  | Proposition de projet (ce site)     | 🔄 En cours  |
| Études préliminaires           | Sept. 2026  | *à préciser*| Document d'analyse                  | ⏳ À venir   |
| Réalisation (POC)              | *à préciser*| *à préciser*| Prototype / POC                     | ⏳ À venir   |
| Présentation + Rapport         | Déc. 2026   | Déc. 2026   | Présentation + Rapport final        | ⏳ À venir   |

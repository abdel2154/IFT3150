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
    **Thème(s)**: Intelligence artificielle appliquée : veille informationnelle et résumé assistés par IA (plateforme interne de services)  
    **Superviseur(s)**: <!-- PLACEHOLDER : nom du superviseur (affiliation) à compléter -->  
    **Collaborateur(s):** Ministère de l'Emploi et de la Solidarité sociale (MESS), Gouvernement du Québec  

## Description du projet

### Contexte

Ce projet est relié à un mandat que je réalise au Ministère de l'Emploi et de la Solidarité sociale (MESS) du Québec, un ministère qui touche à l'emploi, au marché du travail et à la solidarité sociale.

Dans ce genre d'organisation, les analystes et les gestionnaires doivent suivre énormément d'information : actualités, rapports, études, publications. Faire cette veille à la main prend beaucoup de temps.

L'IA (recherche sémantique, *embeddings*, modèles de langue) peut aider à alléger ce travail. Mais comme on est dans un ministère, il faut rester prudent avec la confidentialité et la sécurité des données (voir la section [Contraintes et confidentialité](#contraintes-et-confidentialite)).

### Problématique

Le problème principal, c'est la surcharge d'information :

- il y a beaucoup trop d'articles et de sources à suivre ;
- c'est difficile de repérer vite ce qui est vraiment pertinent ;
- tout lire et tout trier à la main, ça ne tient pas à grande échelle.

La recherche par mots-clés ne suffit pas : elle est lente et elle ne comprend pas vraiment le sens des textes.

### Proposition et objectifs

L'idée, c'est une plateforme interne qui regroupe plusieurs services d'IA, avec une page d'accueil où on choisit le service voulu. Elle est pensée pour des gens qui ne sont pas des experts techniques.

J'ai déjà prototypé un premier service, VeilleIA, qui fait deux choses :

1. La veille : il récupère des articles et garde seulement les plus pertinents, grâce à des *embeddings* calculés en local.
2. Le résumé et l'analyse : il produit un résumé des articles retenus et une analyse plus détaillée à l'aide d'un modèle de langue.

!!! note "Prochaine étape et idées pour plus tard"
    - **Base documentaire** : laisser l'utilisateur garder ses articles et déposer ses propres PDF, pour que l'IA s'appuie dessus dans ses analyses (approche *RAG*). Pas encore fait.
    - **Modèle de langue en local avec Ollama** : faire tourner le résumé en local pour ne plus dépendre d'un service externe (j'ai déjà commencé un petit prototype de résumé local à partir du travail d'un collègue).
    - **Classification de documents** : ranger automatiquement des documents selon un plan de classification.

## Récits d'utilisation

> Quelques exemples de qui utilise l'outil et pour quoi faire.

!!! abstract "Récit 1 : trier les articles par pertinence"
    En tant que conseiller en veille, je veux que le système aille chercher les articles de mes sources (presse et flux RSS, via Inoreader) et me sorte seulement les plus pertinents pour ma thématique, pour ne pas avoir à en lire des centaines.

!!! abstract "Récit 2 : résumer et analyser"
    En tant qu'analyste, une fois les articles pertinents trouvés, je veux un résumé de l'ensemble et, en cliquant sur un article, une analyse plus détaillée (points clés, lien avec le contexte du MESS), pour aller à l'essentiel rapidement.

!!! abstract "Récit 3 : tout au même endroit"
    En tant qu'utilisateur qui n'est pas technique, je veux une page d'accueil simple où je choisis le service et je navigue facilement, pour utiliser l'IA sans avoir à comprendre ce qu'il y a derrière.

!!! abstract "Récits pour plus tard (extensions)"
    - En tant qu'analyste, je veux déposer mes propres PDF dans une base documentaire pour que l'IA s'en serve dans ses analyses. *(Extension : base documentaire / RAG.)*
    

## Comment marche la veille

!!! info "Description Inoreader"
    Inoreader est un service en ligne qui rassemble au même endroit les articles de plein de sources (sites de presse, blogues, etc). On s'abonne à des sources et on les range par dossier. Mon application se connecte à un compte Inoreader qui m'a été fourni pour aller y chercher les articles, au lieu de visiter chaque site un par un.

Le tri par pertinence se fait entièrement avec des *embeddings* calculés en local, sans faire appel à un modèle de langue.

| Étape | Ce que ça fait | Outil |
|-------|----------------|-------|
| 1. Récupération | Aller chercher les articles (titre, extrait, source, date) | API Inoreader  |
| 2. Embeddings | Transformer en vecteurs, en local, chaque article et chaque thématique | Xenova/multilingual-e5-small (ONNX, 384 dimensions), `@huggingface/transformers`, dans Node.js |
| 3. Score | Mesurer à quel point l'article colle à la thématique (et à la requête) | Similarité cosinus |
| 4. Filtrage et tri | Garder les articles au-dessus d'un seuil, puis trier par pertinence (un article peut toucher plusieurs thématiques) | Seuil fixé à 0,72 |

```text
Inoreader → Articles → Embeddings locaux → Similarité cosinus → Seuil + tri → Top N
```

Quand l'utilisateur ajoute une recherche en langage naturel, le score mélange la proximité avec la thématique et la proximité avec cette recherche.

## Côté technique

- **Front-end** : React 19 et TypeScript, Vite, Tailwind CSS 3.
- **Back-end** : Node.js 22 et Express 5, découpé en routes, services et mocks.
- **Modèle d'embeddings** : tourne 100 % en local, chargé au démarrage du serveur.
- **Modèle de langue** : GPT-4o mini (API OpenAI).
- **Mode démo** : des données factices pour tester l'interface sans brancher les API.

## Contraintes et confidentialité

!!! warning "Ce qui sort et ce qui reste en local"
    Il y a deux cas à ne pas mélanger :

    - La veille (ce qu'on a aujourd'hui) : tout le tri se fait en local avec les *embeddings*. GPT-4o mini (OpenAI) ne voit que des articles de presse publics venus d'Inoreader. Comme ces articles sont déjà publics, les envoyer à OpenAI ne pose pas de souci de confidentialité.
    - Les documents internes : pour l'instant, on ne les traite pas. La fonction pour résumer des documents déposés par l'utilisateur est en train d'être développée.

Pour cette fonction de résumé de documents (à venir), l'utilisateur pourra choisir le modèle avec un petit sélecteur : soit un modèle local (par exemple avec Ollama), soit un modèle externe via une clé API. Comme ça, un document sensible peut être résumé entièrement en local, sans rien envoyer dehors.

Deux autres choses :

- Pour Inoreader, je passe par OAuth 2.0 : aucun mot de passe n'est stocké.
- Mes choix tiennent compte de la qualité, de la vitesse, du coût, de la sécurité et de la clarté, pas juste de la performance.

### Technologies utilisées

- Inoreader (pour récupérer les sources) et OAuth 2.0.
- Xenova/multilingual-e5-small (embeddings) via @huggingface/transformers, en local.
- GPT-4o mini (API OpenAI) pour le résumé et l'analyse des articles.
- React, TypeScript, Vite, Tailwind, Node.js, Express.

### Comment je m'y prends

- J'avance par petites preuves de concept : comprendre un besoin, prototyper, puis évaluer.
- Je maquette l'interface (dans Figma) avant de coder.
- Je choisis mes outils en cherchant un bon équilibre entre souveraineté des données, coût et performance.

### Validation et évaluation

Je n'ai pas encore d'évaluation chiffrée complète, mais j'ai commencé à vérifier les résultats à la main.

- Qualité des résumés : je relis les résumés générés pour juger s'ils sont fidèles et utiles, et je note ceux qui posent problème (infos manquantes, hors sujet, trop longs). Ça me sert à repérer ce qu'il faut améliorer.
- Pertinence du tri (prévu) : étiqueter moi-même un petit ensemble d'articles (pertinent ou non), comparer avec ce que le système garde, et en tirer une précision. Ça permettra de vérifier le tri et de justifier ou d'ajuster le seuil de 0,72.

À ça s'ajoutent les retours de mon superviseur.

> Limites que je connais déjà : les flux RSS ne donnent souvent qu'un court extrait ; je n'ai testé qu'un seul modèle d'embeddings ; le tri n'a pas encore de précision chiffrée ; c'est un prototype, jamais mis en production.

## Échéancier

!!! info
    Le suivi complet est disponible dans la page [Suivi de projet](suivi.md).

<!-- PLACEHOLDER : dates et jalons à confirmer avec le plan de cours (session Automne 2026) -->

| Activités                      | Début       | Fin         | Livrable                            | Statut       |
|--------------------------------|-------------|-------------|-------------------------------------|--------------|
| Ouverture de projet            | Sept. 2026  | Sept. 2026  | Proposition de projet (ce site)     | ✅ Terminé   |
| Réalisation (POC VeilleIA)     | Sept. 2026  | Oct. 2026   | Prototype veille + résumé           | 🔄 En cours  |
| Évaluation                     | *à préciser*| *à préciser*| Validation de la pertinence         | ⏳ À venir   |
| Présentation + Rapport         | Déc. 2026   | Déc. 2026   | Présentation + Rapport final        | ⏳ À venir   |

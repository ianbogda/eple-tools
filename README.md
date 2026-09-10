# Roadmap

EPLE Tools évolue progressivement d’une collection d’outils autonomes vers une plateforme métier **modulaire, testable, réutilisable et ouverte aux contributions**.

L’objectif n’est pas seulement de développer davantage d’outils. Il est de construire un socle suffisamment stable pour permettre à d’autres contributeurs d’apporter leurs propres briques sans devoir connaître ou modifier l’ensemble du projet.

---

## 1. Découpler le code métier

Les premières applications ont été développées sous forme de pages HTML/JavaScript autonomes afin de répondre rapidement aux besoins.

La prochaine étape consiste à **séparer la logique métier de l’interface utilisateur**.

```text
Interface utilisateur
        │
        ▼
Module métier
        │
        ▼
Services communs
        │
        ▼
Données
```

Cette évolution doit permettre :

* d’isoler les règles de gestion ;
* de réduire les dépendances entre interface et calculs ;
* de réutiliser une même logique dans plusieurs outils ;
* d’intégrer des tests unitaires ;
* de limiter les régressions ;
* de faciliter la maintenance.

À terme, chaque module métier devra pouvoir être testé indépendamment de l’interface qui l’utilise.

---

## 2. Construire une architecture réellement modulaire et contributive

La modularité doit permettre à EPLE Tools de dépasser le modèle d’une collection d’applications développées par une seule personne.

L’objectif est qu’un contributeur puisse développer une nouvelle fonctionnalité ou un nouvel outil sous la forme d’une **brique autonome respectant un contrat commun**.

Le principe recherché est :

```text
                    EPLE Tools
                        │
                ┌───────┴───────┐
                │   Socle commun │
                └───────┬───────┘
                        │
           ┌────────────┼────────────┐
           │            │            │
           ▼            ▼            ▼
       Module A      Module B      Module C
       officiel      officiel     contributeur
```

Un module ne devra donc pas réinventer :

* la navigation ;
* l’identité utilisateur ;
* la gestion de l’établissement actif ;
* les imports ;
* le stockage ;
* les notifications ;
* les fenêtres de confirmation ;
* les exports ;
* la journalisation ;
* les composants graphiques communs.

Ces services seront fournis par le socle.

### Un contrat de module

Chaque module devra respecter une structure documentée et prévisible.

Par exemple :

```text
modules/
└── recouv-opale/
    ├── module.json
    ├── index.ts
    ├── ui/
    ├── domain/
    ├── services/
    ├── tests/
    ├── assets/
    └── README.md
```

Un manifeste `module.json` pourra notamment déclarer :

```text
identifiant
nom
version
auteur
description
catégorie
permissions
types de données consommés
types de données produits
routes
dépendances
compatibilité avec le socle
```

Le socle pourra ainsi découvrir et charger automatiquement les modules compatibles.

### Un cadre de contribution formalisé

Le dépôt devra progressivement fournir un véritable **kit de développement de module**.

Il comprendra notamment :

* la spécification du contrat de module ;
* un module minimal d’exemple ;
* un squelette de nouveau module ;
* les conventions de nommage ;
* les composants UI disponibles ;
* les API du socle ;
* les formats de données partagés ;
* les règles de sécurité ;
* les règles de versionnement ;
* les tests obligatoires ;
* les critères d’acceptation d’une contribution.

Le parcours recherché pour un contributeur doit devenir :

```text
Cloner le dépôt
      │
      ▼
Créer un module depuis le modèle
      │
      ▼
Développer la logique métier
      │
      ▼
Ajouter les tests
      │
      ▼
Valider automatiquement le contrat
      │
      ▼
Proposer une Pull Request
      │
      ▼
Revue collaborative
      │
      ▼
Intégration dans EPLE Tools
```

L’objectif est qu’un contributeur puisse travailler sur **sa brique métier sans avoir à modifier le cœur de la Toolbox**.

---

## 3. Mutualiser les imports et les données

Aujourd’hui, plusieurs outils exploitent des fichiers provenant des mêmes environnements : Op@le, applications SRH, exports Excel ou CSV.

Une donnée importée dans un outil doit pouvoir être **réutilisée par tous les modules qui en ont besoin**.

Par exemple :

```text
                Import YBALAC
                      │
                Normalisation
                      │
                      ▼
              Données partagées
                      │
        ┌─────────────┼─────────────┐
        ▼             ▼             ▼
 Recouv'Op@le    Tableau de bord   Autre module
```

La cible est de disposer :

* d’un composant unique d’import Excel/CSV ;
* d’importateurs spécialisés par type d’export ;
* d’un modèle de données partagé ;
* d’une normalisation des colonnes ;
* d’un stockage commun ;
* d’un mécanisme de validation ;
* d’une traçabilité des imports.

Le principe devient :

> **Importer une fois, exploiter plusieurs fois.**

Cette architecture permettra également à un module contributeur de déclarer simplement :

> « J’ai besoin des données YBALAC »

plutôt que de devoir réimplémenter son propre importeur YBALAC.

---

## 4. Proposer plusieurs modes de déploiement

Tous les établissements n’ont pas les mêmes besoins ni les mêmes capacités techniques.

EPLE Tools doit donc pouvoir fonctionner selon trois architectures.

### Mode 1 — Standalone local

Une application autonome utilisable directement sur un poste.

```text
Un fichier HTML
       │
       ├── interface
       ├── modules métier
       ├── bibliothèques
       └── stockage local
```

Ce mode est adapté :

* à un usage individuel ;
* aux tests ;
* aux postes sans infrastructure particulière ;
* à une diffusion rapide ;
* à une utilisation sur clé USB.

Le fichier autonome devra autant que possible être **généré à partir des mêmes modules que les autres versions**, et non maintenu comme une application différente.

---

### Mode 2 — Réseau local

Version destinée à un établissement ou à une agence comptable.

```text
Navigateurs
     │
     ▼
Mini serveur web
     │
     ├── EPLE Tools
     ├── API locale
     └── SQLite
```

Cette version doit permettre :

* plusieurs utilisateurs ;
* une identité utilisateur ;
* un établissement actif ;
* un espace de données partagé ;
* la persistance des imports ;
* le partage des données entre modules ;
* la gestion des sauvegardes.

Elle constitue la cible naturelle pour un établissement ou une agence comptable souhaitant partager les outils sur son réseau.

---

### Mode 3 — Version hébergée

Version destinée à un usage multi-utilisateurs et multi-EPLE plus important.

```text
Navigateurs
     │
     ▼
Frontend EPLE Tools
     │
     ▼
Node.js / TypeScript
     │
     ▼
API métier
     │
     ▼
PostgreSQL
```

Cette architecture doit notamment permettre :

* plusieurs établissements ;
* plusieurs utilisateurs ;
* des rôles et droits différenciés ;
* une authentification centralisée ;
* une base de données commune ;
* la gestion des historiques ;
* des sauvegardes structurées ;
* une meilleure gestion des accès concurrents ;
* des tests automatisés ;
* un déploiement industrialisé.

---

# Une seule Toolbox, plusieurs modes d’exécution

L’objectif n’est pas de maintenir trois applications différentes.

Les trois modes doivent partager le maximum de code.

```text
                    Modules EPLE Tools
                           │
              ┌────────────┼────────────┐
              │            │            │
              ▼            ▼            ▼
         Standalone     Réseau local    Hébergé
              │            │            │
              ▼            ▼            ▼
        Stockage local    SQLite     PostgreSQL
```

Le **code métier**, les contrats de modules et autant que possible les composants d’interface doivent rester communs.

Ce sont principalement les couches de stockage, d’authentification et de déploiement qui doivent varier.

---

# Vers un écosystème EPLE Tools

À terme, l’architecture recherchée peut être résumée ainsi :

```text
                       EPLE TOOLS
                           │
                    ┌──────┴──────┐
                    │ SOCLE COMMUN │
                    └──────┬──────┘
                           │
       ┌───────────────────┼───────────────────┐
       │                   │                   │
       ▼                   ▼                   ▼
    Identité            Données             Services
 Établissements         partagées            communs
       │                   │                   │
       └───────────────────┼───────────────────┘
                           │
                    CONTRAT DE MODULE
                           │
          ┌────────────────┼────────────────┐
          │                │                │
          ▼                ▼                ▼
       Modules          Modules          Modules
       natifs        contributeurs     expérimentaux
          │                │                │
          └────────────────┼────────────────┘
                           │
                     Tests automatisés
                           │
                     Revue collaborative
                           │
                        Release
```

La modularité devient ainsi autant un **choix d’architecture** qu’un **mode de gouvernance du projet**.

---

# Priorités

La trajectoire privilégiée est désormais :

**1. Stabiliser les outils existants.**

**2. Extraire progressivement leur logique métier des pages HTML.**

**3. Définir le contrat standard d’un module EPLE Tools.**

**4. Créer un module minimal de référence et un kit de démarrage pour les contributeurs.**

**5. Mettre en place les tests unitaires et les contrôles automatiques de conformité des modules.**

**6. Unifier les imports et construire le modèle de données partagé.**

**7. Faire migrer progressivement les outils existants vers cette architecture.**

**8. Consolider la version réseau locale avec serveur léger et SQLite.**

**9. Industrialiser la construction automatique de la version standalone.**

**10. Préparer la version hébergée Node.js / TypeScript / PostgreSQL.**

**11. Documenter le processus de contribution et de revue des Pull Requests.**

**12. Permettre progressivement à la communauté de proposer et maintenir ses propres modules.**

La réussite du projet ne se mesurera donc pas uniquement au nombre d’outils disponibles.

Elle se mesurera aussi à la capacité d’un nouveau contributeur à **comprendre le cadre, développer une brique métier, la tester et la proposer au projet sans avoir à modifier le cœur d’EPLE Tools**.

<p align="center">
  <img src="docs/images/Palks_Studio.png" alt="Palks Studio" width="600">
</p>

> 🇫🇷 Français | [🇬🇧 English](./README.md)

![License](https://img.shields.io/badge/License-LICENSE.txt-lightgreen.svg)

# Présentation du projet

Ce dépôt présente l’architecture complète d’un site web orienté bien-être naturel,
conçu sans CMS, sans SaaS, sans cookies et sans backend exposé.

L’ensemble du système fonctionne exclusivement sur un hébergement mutualisé,
sans infrastructure dédiée ni services managés,
à l’exception du prestataire de paiement.

Le projet repose sur une approche volontairement minimaliste et autonome :
aucune dépendance critique externe, aucune collecte de données, et une
infrastructure pensée pour fonctionner durablement sur un hébergement mutualisé.

---

## Principes et objectifs

Le projet a été conçu autour de principes clairs :  

- autonomie totale de l’infrastructure  
- absence de services tiers non indispensables  
- aucune dépendance à un CMS ou à un framework serveur  
- aucune collecte de données utilisateur  
- stabilité et maintenabilité sur le long terme  
- surface d’attaque réduite au strict minimum

L’objectif n’était pas de maximiser la complexité technique,
mais de construire un système robuste, lisible et prévisible,
capable de fonctionner de manière fiable sans supervision constante.

---

## Architecture générale

Le projet est structuré autour de trois sous-systèmes distincts,
séparés volontairement par rôle et niveau d’exposition.

Cette organisation permet de limiter la surface d’attaque,
de clarifier les responsabilités et de garantir une maintenance simple
sur le long terme.

L’architecture repose sur les blocs suivants :  

- `site/` : couche publique statique (HTML, assets, JavaScript)  
- `web/` : logique serveur sensible (paiement, facturation, distribution)  
- `worker/` : traitements internes asynchrones (automatisation, maintenance)

Chaque sous-système est indépendant sur le plan logique,
mais interagit de manière contrôlée avec les autres.

---

## Arborescence du projet

```
project-root/
│
├── README_FR.md                   → Présentation générale du projet et de son architecture
├── LICENSE                        → Conditions d’utilisation et cadre juridique du projet
│
├── worker/
│    ├── main.py                   → Point d’entrée du worker (cron / déclencheur PHP)
│    ├── core.py                   → Logique principale du worker
│    ├── task_a.py                 → Traitement automatisé
│    ├── task_b.py                 → Maintenance des journaux
│    ├── bridge.php                → Pont PHP → Python
│    ├── config.json               → Fichier de configuration (anonymisé)
│    ├── state_a.json              → Registre des identifiants traités
│    ├── cron_log.txt              → Sortie des exécutions cron
│    │
│    ├── data/
│    │   └── data_a.json           → Source de données du worker
│    │
│    ├── logs/
│    │   └── unmatched.txt         → Journal des entrées non reconnues
│    │
│    └── tmp/
│        └── state.txt             → Fichier de contrôle / état
│
├── web/
│    ├── endpoint_c.php            → Point d’entrée pour les téléchargements sécurisés
│    ├── state.txt                 → Fichier de contrôle d’état du système
│    ├── .htaccess                 → Règles d’accès et de sécurité
│    │
│    └── pdf/
│        ├── .htaccess             → Règles d’accès internes
│        ├── dompdf/               → Librairie de génération de PDF
│        ├── template_invoice.html → Modèle HTML de facture
│        ├── invoices/             → Factures générées
│        ├── recettes/             → Données de recettes
│        ├── endpoint_d.php        → Initialisation du paiement
│        ├── endpoint_e.php        → Réception des événements de paiement
│        ├── success.html          → Page affichée après paiement réussi
│        ├── cancel.html           → Page affichée après paiement annulé
│        ├── data_c.json           → Registre des tokens de téléchargement
│        ├── counter.json          → Compteur de numérotation des factures
│        ├── get_counter.php       → Génération du prochain numéro de facture
│        ├── update_counter.php    → Mise à jour du compteur de facturation
│        ├── lib_pdf.php           → Fonctions de génération PDF
│        ├── lib_html.php          → Fonctions utilitaires HTML
│        └── lib_counter.php       → Fonctions liées au compteur de factures
│
└── site/
     ├── assets/
     │   └── css/                  → Feuilles de style (externe optionnel)
     │
     ├── images/                   → Images du site (logos et favicons inclus)
     │
     ├── pages/                    → Pages HTML du site (articles et contenus)
     │   └── *.html
     │
     ├── logs/
     │   ├── errors.txt            → Journal des erreurs
     │   └── interactions.txt      → Journal des interactions
     │
     ├── tmp/state.txt             → Fichier de contrôle / état
     │
     ├── dl/                       → Store
     │
     ├── index.html                → Page d’accueil
     ├── data_b.json               → Journal des soumissions d’avis
     ├── endpoint_a.php            → Webhook de paiement
     ├── task_a.py                 → Script de nettoyage des logs
     │
     ├── .htaccess                 → Règles d’accès principales
     │
     ├── robots.txt                → Règles d’indexation pour les moteurs de recherche
     ├── sitemap.xml               → Plan du site pour l’indexation
     ├── site.webmanifest          → Manifest PWA du site
     ├── endpoint_b.php            → Envoi des avis depuis le formulaire
     ├── data_a.json               → Jetons temporaires liés aux téléchargements
     ├── index_hero.js             → Script d’initialisation du contenu hebdomadaire
     ├── weekly-2025               → Données hebdomadaires – année 2025
     └── weekly-2026               → Données hebdomadaires – année 2026
```


---

### `site/` — couche publique

Ce dossier contient exclusivement le site public.

Il s’agit d’un site statique composé de fichiers HTML indépendants,
accompagnés de feuilles de style et de scripts JavaScript légers.
Aucune logique serveur critique n’est exposée depuis cette couche.

Le site public est le seul point de contact avec le navigateur.
Il ne stocke aucune donnée sensible et ne dépend d’aucun service externe.

---

### `web/` — logique serveur sensible

Ce sous-système regroupe l’ensemble des traitements serveur
liés aux paiements, à la facturation et à la distribution des fichiers.

Il contient notamment :  

- la gestion des paiements via un prestataire externe  
- la génération automatique des factures et recettes  
- le moteur de distribution sécurisée des fichiers  
- les mécanismes de contrôle et d’expiration des accès

Les points d’entrée sont volontairement limités et protégés.
Les noms de fichiers et d’endpoints ont été abstraits afin de réduire
les risques liés à l’exposition publique.

---

### `worker/` — automatisation interne

Le dossier `worker/` contient les traitements internes exécutés
en arrière-plan, sans aucune exposition publique.

Ces scripts sont déclenchés uniquement via des tâches planifiées
ou des appels serveur internes.

Ils assurent notamment :  

- l’automatisation de certaines opérations  
- la maintenance des journaux  
- le nettoyage des fichiers temporaires  
- la gestion de données internes au format local

Ce choix permet d’éviter tout backend exposé
et de conserver une architecture silencieuse et maîtrisée.

---

## 1. Site public statique

Le site public repose sur une architecture volontairement simple et légère,
entièrement composée de fichiers HTML indépendants.

Aucun CMS, aucun framework, aucun builder et aucun CDN ne sont utilisés.
Chaque page est conçue comme une unité autonome, stable et réutilisable.

### Caractéristiques principales

- site entièrement statique (HTML + CSS autonome)  
- aucune dépendance externe critique  
- navigation rapide et fluide  
- thème pensé pour le confort visuel  
- structure simple et prévisible  
- fichiers exportables et réutilisables sans adaptation

Ce choix permet de garantir un site rapide, robuste et facile à maintenir,
avec un risque de panne extrêmement réduit.

Le site intègre également des scripts légers d’affichage dynamique,
permettant de faire évoluer certains contenus de manière périodique
sans backend ni stockage côté client.

Les feuilles de style peuvent être intégrées de manière autonome
ou externalisées de façon optionnelle,
sans dépendance critique au chargement externe.

---

## 2. Automatisation interne (worker)

Le projet intègre un système d’automatisation interne,
sans exposer de backend applicatif au public.

Aucun serveur Python n’est accessible depuis l’extérieur
(pas de framework web, pas d’API publique, pas de runtime persistant).
Les traitements sont exécutés exclusivement en interne.

### Fonctionnement

- scripts Python exécutés via des tâches planifiées (Cron)  
- déclenchements contrôlés depuis le serveur  
- données stockées localement au format JSON  
- aucune communication sortante non nécessaire  
- aucune exposition réseau directe

Ce choix permet de conserver une architecture silencieuse,
maîtrisée et conforme, tout en assurant les besoins
d’automatisation et de maintenance du projet.

L’absence de backend exposé réduit fortement la surface d’attaque
et simplifie la supervision sur le long terme.

---

## 2 bis. Assistant interne et moteur de réponse

Le projet intègre un assistant interne destiné à orienter les utilisateurs
et à répondre à des questions ciblées, sans exposer de logique applicative
complexe côté public.

Cet assistant repose sur un moteur de réponse autonome,
implémenté en Python et alimenté par une base de données locale
structurée au format JSON.
Il analyse les requêtes reçues, identifie des correspondances
par mots-clés et catégories, puis renvoie des réponses adaptées.

### Principes de fonctionnement

- moteur de réponse exécuté côté serveur  
- logique déterministe et maîtrisée  
- aucune dépendance à un service d’IA externe  
- aucune collecte ou conservation de données personnelles  
- journalisation locale des requêtes non reconnues  
- traçabilité des erreurs techniques à des fins de maintenance

L’assistant ne délivre aucun conseil médical
et se limite strictement à des contenus informatifs et orientatifs,
conformément au périmètre du projet.

Ce choix permet de proposer une aide contextualisée
tout en conservant une architecture sobre,
prévisible et respectueuse des contraintes de sécurité et de conformité.

---

## 3. Paiement, facturation et distribution

Le projet intègre un système de paiement et de distribution
entièrement géré côté serveur, sans intermédiaire d’automatisation externe.

Les paiements sont initiés via un prestataire dédié,
puis traités par des scripts internes déclenchés par événements.
Aucune donnée sensible n’est stockée côté site public.

### Pipeline général

- déclenchement du paiement via une page dédiée  
- réception et traitement des événements serveur  
- génération automatique des factures au format PDF  
- attribution d’un numéro de facture unique et séquentiel  
- classement automatique des documents par année et par mois  
- préparation des accès de téléchargement sécurisés

L’ensemble du processus est automatisé et ne dépend
d’aucune plateforme tierce d’orchestration.

### Distribution sécurisée des fichiers

La distribution des fichiers numériques repose sur un moteur interne dédié,
conçu pour éviter toute exposition directe des ressources.

Les fichiers ne sont jamais accessibles par URL publique.
L’accès est conditionné à des liens temporaires à usage unique,
générés dynamiquement après validation côté serveur.

Les contrôles mis en place incluent notamment :  

- expiration automatique des accès  
- validation du contexte de téléchargement  
- contrôle d’intégrité des accès  
- invalidation immédiate après usage  
- journalisation horodatée des accès effectifs

Le système permet également de distinguer
une simple consultation de lien
d’un téléchargement réellement effectué,
avec notification côté administration.

L’ensemble du mécanisme fonctionne sans service tiers
et sans exposition de logique sensible côté site public.

---

## 4. Sécurité et protection structurelle

La sécurité du projet repose avant tout sur des choix structurels
simples et stricts, plutôt que sur l’empilement de solutions externes.

L’architecture a été pensée pour limiter volontairement
la surface d’attaque et réduire les points d’entrée exploitables.

### Mesures mises en place

- cloisonnement strict entre site public et logique serveur  
- règles d’accès renforcées au niveau serveur  
- désactivation complète du listing des répertoires  
- protection des fichiers sensibles (données, scripts, journaux)  
- zones critiques rendues inaccessibles par défaut  
- absence d’URL directes vers les ressources privées

Les noms de fichiers, d’endpoints et de données sensibles
ont été volontairement abstraits afin de limiter
les attaques opportunistes et les scans automatisés.

Cette approche privilégie la simplicité, la lisibilité
et une sécurité passive durable.

---

## 5. Conformité RGPD et sobriété des données

Le projet a été conçu dès l’origine avec une approche de sobriété maximale
en matière de données et de conformité réglementaire.

Aucune donnée personnelle n’est collectée à des fins de suivi,
d’analyse ou de profilage.
Le site ne repose sur aucun mécanisme de traçage.

### Principes appliqués

- absence totale de cookies  
- absence de traceurs ou de pixels tiers  
- absence d’outils d’analytics externes  
- absence de stockage local côté navigateur  
- absence de comptes utilisateurs  
- aucune collecte de données à des fins marketing

Les seules données manipulées par le système
le sont de manière strictement fonctionnelle,
limitée dans le temps et stockée localement côté serveur.

Cette approche permet une conformité RGPD native,
sans bannière intrusive ni gestion de consentement,
tout en respectant le principe de minimisation des données.

---

## 6. Choix techniques et durabilité

Les choix techniques effectués dans ce projet ont été guidés
par un objectif de durabilité plutôt que par la recherche
de complexité ou de nouveauté.

L’architecture ne repose sur aucun framework serveur,
aucun runtime applicatif persistant et aucune dépendance lourde.
Les composants utilisés sont volontairement simples,
stables et éprouvés.

### Principes retenus

- absence de CMS et de frameworks serveur  
- absence de dépendances à maintenir en continu  
- utilisation de formats simples et pérennes (HTML, JSON, Python)  
- logique applicative lisible et auditable  
- compatibilité avec un hébergement mutualisé standard

Ce choix permet de réduire drastiquement les besoins de maintenance,
d’éviter les ruptures liées aux mises à jour
et de garantir une stabilité maximale sur le long terme.

L’objectif n’est pas la sophistication technique,
mais la fiabilité, la prévisibilité et la maîtrise complète du système.

---

## Notes de sécurité et divulgation

Ce dépôt présente une vue fidèle de l’architecture logique du projet,
tout en respectant des principes de divulgation responsable.

Certains noms de fichiers, d’endpoints et de structures
ont été volontairement abstraits ou modifiés
afin de limiter toute exploitation directe.

Aucune clé, aucun secret, aucune donnée réelle
et aucun chemin de production sensible
ne sont présents dans ce dépôt.

La structure exposée vise à documenter les choix techniques
et l’organisation du système,
sans reproduire à l’identique l’environnement de production.

---

## Communication et maintenance automatisée

Le projet intègre des mécanismes de communication volontairement limités
et maîtrisés.

Les notifications par e-mail sont utilisées uniquement
pour confirmer la bonne réception des messages
ou signaler des événements techniques importants.
Les réponses aux utilisateurs sont traitées manuellement,
par choix, afin de préserver une interaction humaine.

Par ailleurs, des scripts internes assurent la maintenance automatique :  

- nettoyage régulier des fichiers temporaires  
- purge des journaux et données expirées  
- maintien d’un environnement propre et stable dans le temps

---

## Conclusion

Ce projet illustre la conception d’un site web complet,
autonome et sécurisé,
sans dépendance à des plateformes externes
et sans exposition inutile de backend.

Il démontre qu’une architecture simple,
bien pensée et maîtrisée
peut répondre à des besoins réels
tout en restant durable, performante et conforme.

L’ensemble a été conçu comme un système capable
de fonctionner de manière fiable sur le long terme,
avec un minimum de maintenance et une surface d’attaque réduite.

---

## Crédits et licence

Ce projet est une création originale de **Palks Studio**.

Le code et l’architecture présentés dans ce dépôt sont fournis
à des fins de démonstration technique et de portfolio.
Toute réutilisation, redistribution ou exploitation publique
sans autorisation préalable n’est pas autorisée.

L’usage personnel et professionnel à des fins d’évaluation,
d’étude ou d’inspiration est autorisé.

Voir le fichier LICENSE pour les conditions d’utilisation.

**Palks Studio**  
Créateur indépendant d’outils digitaux autonomes et durables.

- GitHub : https://github.com/Palks-Studio  
- Gumroad : https://palks.gumroad.com  
- Itch.io : http://palks-studio.itch.io  
- Ko-fi : https://ko-fi.com/palksstudio  
- Twitter (X) : https://x.com/Palks_Studio

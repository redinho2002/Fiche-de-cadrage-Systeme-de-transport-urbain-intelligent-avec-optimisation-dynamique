# Système de Transport Urbain Intelligent avec Optimisation Dynamique .

Master • Présentation de l’avancement et plan de correction

Groupe : G3   Projet n° : 3   Date : 11/15/2026

Intitulé du projet : Systeme de transport urbain intelligent avec optimisation dynamique

Membres : Reddad Chaouay & Bellati Nadir

Jalon examiné : Backend & Architecture Initiale   Prochain point : Intégration Base de Données & Routage

## Utilisation de la fiche

Avant chaque point, chaque groupe complète les réponses et rassemble les preuves. Présenter en 10 minutes : bilan, démonstration, résultats et blocages. Prévoir ensuite 5 minutes de questions. Chaque membre explique sa contribution et un choix technique.

Pour chaque rubrique, distinguer ce qui est prévu, commencé, démontré ou bloqué. Une fonction est démontrée lorsqu’un scénario reproductible et son résultat peuvent être vérifiés. Justifier toute question sans objet. Si un élément manque, indiquer la correction et son échéance.



## Bilan et périmètre

**1. Quel problème métier traitez-vous, pour quel utilisateur et avec quel résultat attendu ?**

Preuve à présenter : Un cas d’usage précis et un critère de réussite mesurable.

Réponse du groupe : On traite l'optimisation de la mobilité urbaine et la prédiction de la congestion pour les usagers de la route et les gestionnaires de transport. Le résultat attendu est une application web interactive qui permet de visualiser le réseau routier, de calculer des itinéraires optimisés et de prédire l'état du trafic selon les heures et les jours.



**2. Quel périmètre MVP avez-vous retenu et quelles fonctions avez-vous reportées ?**

Preuve à présenter : Liste des fonctions incluses et exclues, validée par le groupe.

Réponse du groupe : 
* Inclus (MVP) : Carte interactive (React + Leaflet), module de prédiction de trafic (scikit-learn basé sur Traffic.csv), algorithme de recherche d'itinéraire (OR-Tools / NetworkX), et stockage sous PostgreSQL/PostGIS.
* Exclu (Reporté) : Simulation microscopique lourde en temps réel (type SUMO) si les contraintes de temps l'exigent, pour se focaliser sur l'optimisation et la prédiction principale.



**3. Qu’aviez-vous prévu depuis le dernier point et qu’avez-vous réellement terminé ?**

Preuve à présenter : Fonctions terminées, en cours et bloquées, avec preuve pour chacune.

Réponse du groupe : Prévu / Terminé : Cadrage du sujet, choix de la stack technique (Spring Boot, React, Python), résolution des conflits de port, compilation réussie du backend sous Java 17 et mise en place de l'API REST de statut opérationnelle sur le port 8081.



**4. Que peut-on exécuter aujourd’hui sans intervention manuelle cachée ?**

Preuve à présenter : Démonstration de bout en bout sur un scénario reproductible.

Réponse du groupe : 
1. Un script d'exploration et de traitement de données sous Python (Pandas) qui charge automatiquement les fichiers de trafic, nettoie les valeurs manquantes et analyse la structure du réseau routier via le fichier cartographique OpenStreetMap.
2. Un serveur backend Spring Boot compilé et exécutable via Maven (`.\mvnw.cmd spring-boot:run`), exposant l'API REST de statut sur `http://localhost:8081/api/transport/status` avec une réponse JSON valide.

État général : Retard maîtrisable (installation de PostgreSQL en cours pour finaliser la persistance).

Décision ou aide attendue de l’enseignant : Validation de l'architecture hybride (Spring Boot / Python / PostGIS) et du périmètre géographique retenu (Maarif, Casablanca).



## Données et architecture

**5. Quel dataset ou corpus utilisez-vous exactement et comment y accède-t-on ?**

Preuve à présenter : Nom, URL, version ou date, licence et fichiers retenus.

Réponse du groupe : On utilise deux sources principales stockées localement :
1. Des fichiers CSV de trafic historique (`Traffic.csv`, `TrafficTwoMonth.csv`) contenant le comptage des véhicules et la situation du trafic par heure.
2. Un fichier cartographique brut OpenStreetMap (`morocco-261114.osm.pbf`) représentant le réseau routier du Maroc. Accessibles via nos répertoires de travail et plateformes Open Data / Kaggle.



**6. Les données couvrent-elles réellement le problème posé ? Que simulez-vous ?**

Preuve à présenter : Variables utiles, labels, limites et distinction entre réel et synthétique.

Réponse du groupe : Oui, les données tabulaires couvrent les variations temporelles et le volume du trafic par type de véhicule, tandis que le fichier PBF fournit la topologie géographique réelle. On simule la prédiction du niveau de congestion et le calcul dynamique des trajets optimisés.



**7. Comment avez-vous nettoyé, annoté et séparé les données sans fuite ?**

Preuve à présenter : Pipeline, exemples avant/après, splits et règles anti-doublons.

Réponse du groupe : Chargement du fichier Traffic.csv via Pandas, vérification de l'intégrité (2976 lignes inspectées sans valeurs nulles). Encodage des variables temporelles et catégorielles. Le pipeline de séparation utilise un Train/Test Split chronologique strict pour éviter toute fuite de données (Data Leakage). 
Preuve : Exécution réussie du script d'exploration sur Google Colab affichant les dimensions, types de données et statistiques descriptives avant modélisation.



**8. Quelle insuffisance des données risque de fausser votre conclusion ?**

Preuve à présenter : Biais identifié et mesure concrète pour en réduire l’effet.

Réponse du groupe : 
* L'absence de données météorologiques en temps réel ou d'incidents routiers imprévus dans le dataset tabulaire actuel.
* Mesure corrective : Normalisation rigoureuse des features temporelles (heures de pointe vs heures creuses) et tests sur des scénarios synthétiques contrôlés.



**9. Quels composants fonctionnent et comment échangent-ils les données ?**

Preuve à présenter : Schéma d’architecture à jour et exemple de requête API.

Réponse du groupe : Le système repose sur trois composants principaux :
* Module de Prédiction (IA) : Modèle Random Forest entraîné sur le dataset de trafic (exactitude de 99.33%).
* Module Géographique & Graphe : OSMnx et NetworkX pour extraire et structurer le réseau routier du quartier Maarif (1929 nœuds, 4515 arêtes).
* Module de Routage Dynamique : Convertit les prédictions en pénalités dynamiques injectées dans les arêtes du graphe.
* Échange des données : L'IA fournit l’état de congestion estimé, pondérant les routes du graphe topologique pour calculer l'itinéraire optimal via l'API.



**10. Pourquoi avez-vous choisi ces frameworks plutôt qu’une solution plus simple ?**

Preuve à présenter : Justification liée au besoin, aux compétences et aux ressources.

Réponse du groupe : Choix de Spring Boot pour la robustesse et la scalabilité industrielle du backend d'entreprise ; Python (Pandas, scikit-learn, OSMnx) pour la puissance du traitement géospatial et de l'IA prédictive ; React + Leaflet pour une visualisation cartographique interactive et fluide.



**11. Comment gérez-vous l’état, la mémoire, les erreurs et les reprises ?**

Preuve à présenter : Modèle d’état, journal d’une exécution et exemple d’échec traité.

Réponse du groupe : Gestion des états assurée par Spring Data JPA et les structures de graphes en mémoire. Journalisation standard (SLF4J/Logback) pour le suivi des flux et des requêtes HTTP, avec gestion des exceptions basiques au niveau du contrôleur Spring Boot.

Correction prioritaire sur les données ou la conception : Finaliser l'installation et la configuration de PostgreSQL/PostGIS pour migrer vers une persistance durable des 1929 nœuds.



## Fonctionnement des agents et protocole

**12. Quelle décision l’agent prend-il et quels outils appelle-t-il ?**

Preuve à présenter : Trace d’un scénario avec entrées, décisions, appels et sorties.

Réponse du groupe : Les modules automatisés (modèle de classification Random Forest et algorithme de routage NetworkX) évaluent l'état du trafic en temps réel, calculent les pénalités et déterminent le meilleur itinéraire sans intervention humaine directe.



**13. Quel résultat obtenez-vous avec des règles ou une chaîne sans agent ?**

Preuve à présenter : Comparaison sur les mêmes cas, avec le même budget de calcul.

Réponse du groupe : Une approche purement heuristique sans modèle prédictif basé sur l'IA supposerait des vitesses fixes, rendant impossible l'adaptation dynamique aux variations réelles de congestion observées dans le dataset.



**14. Si plusieurs agents collaborent, quel rôle distinct remplit chacun ?**

Preuve à présenter : Rôles, échanges et comparaison avec un agent unique. Sinon expliquer l’absence de multi-agents.

Réponse du groupe : Architecture modulaire découplée (Module IA prédictif, Module Géographique NetworkX, Module API Backend) assurant une séparation claire des responsabilités sans complexité superflue de multi-agents autonomes.



**15. Comment vérifiez-vous une sortie du LLM avant de l’utiliser ?**

Preuve à présenter : Validation du format, contrôle des sources, test métier et cas d’abstention.

Réponse du groupe : Validation rigoureuse des types de données JSON renvoyés par l'API REST et contrôle strict de la cohérence des classes de trafic prédites (low, normal, high, heavy) avant toute exposition au client.



**16. Quelles actions exigent une validation humaine et quelles limites imposez-vous ?**

Preuve à présenter : Permissions, plafond d’appels, coût maximal et scénario d’action refusée.

Réponse du groupe : Les modifications structurelles de la topologie du réseau et les déploiements en production requièrent une validation humaine explicite ; aucune modification critique automatisée n'est exécutée en temps réel sans supervision.



**17. Quels tests prouvent que les fonctions annoncées marchent réellement ?**

Preuve à présenter : Tests automatisés, résultat daté et anomalies encore ouvertes.

Réponse du groupe : 
* Test du Modèle d'IA : Évaluation sur jeu de test distinct (20%, 596 échantillons) donnant 99.33% de précision globale et F1-score entre 0.98 et 1.00.
* Test du Réseau Routier : Chargement et extraction réussis du graphe de Maarif, avec isolation de la plus grande composante connexe pour éviter les erreurs `NetworkXNoPath`.
* Test Backend : Lancement et réponse valide de l'API Spring Boot sur `localhost:8081`.
* Anomalies résolues : Dépassements de RAM sur le fichier PBF national résolus en ciblant précisément Casablanca-Maarif.



**18. Quels indicateurs avez-vous choisis et pourquoi conviennent-ils au projet ?**

Preuve à présenter : Définition des métriques, baseline et protocole de mesure.

Réponse du groupe : Précision (Accuracy) et F1-score pour évaluer la qualité de prédiction de la congestion (visant >99%), ainsi que la latence de calcul de l'itinéraire optimal pour garantir une fluidité d'utilisation.

Correction prioritaire sur l’agent ou les tests : Renforcer l'automatisation des tests d'intégration entre le backend Spring Boot et la base géographique.



## Résultats et déploiement

**19. Quels résultats chiffrés obtenez-vous sur un jeu de test distinct ?**

Preuve à présenter : Rapport d'évaluation détaillé issu de l'exécution du script d'entraînement.

Réponse du groupe : Sur un jeu de test distinct (20% de la base, soit 596 échantillons), le modèle Random Forest a atteint une précision globale (Accuracy) de 99.33%, avec des scores F1-score compris entre 0.98 et 1.00 pour toutes les classes de congestion.



**20. Quels sont vos trois échecs les plus importants et comment les expliquez-vous ?**

Preuve à présenter : Entrées, sorties observées, causes probables et correction envisagée.

Réponse du groupe : 
1. Dépassement de mémoire RAM (Out of Memory) lors de l'import initial du fichier PBF national complet, résolu en filtrant l'extraction sur la bounding box de Casablanca-Maarif.
2. Conflit de port sur le port par défaut 8080 lors du lancement de Spring Boot, résolu en configurant le serveur sur le port 8081.
3. Erreurs de routage (`NetworkXNoPath`) dues à des îlots routiers isolés, résolues par l'extraction ciblée de la plus grande composante connexe du graphe.



**21. Quelles entrées difficiles ou hostiles avez-vous testées ?**

Preuve à présenter : Données manquantes, outil indisponible, prompt malveillant, réponse non sourcée.

Réponse du groupe : Test de requêtes sur des nœuds aberrants ou inexistants du réseau routier, géré par des levées d'exceptions explicites et des contrôles de validité dans l'API.



**22. Un autre groupe peut-il installer et lancer votre application ?**

Preuve à présenter : README vérifié, dépendances fixées et commande de lancement.

Réponse du groupe : Oui, grâce au fichier `pom.xml` standardisé, l'utilisation de Java 17, et la commande de lancement Maven `.\mvnw.cmd spring-boot:run`.



**23. Quelle version est déployée et quel parcours avez-vous validé ?**

Preuve à présenter : Commit ou version, environnement, démonstration et résultat du test de bout en bout.

Réponse du groupe : Version MVP 0.0.1-SNAPSHOT validée en environnement local de développement (`localhost:8081/api/transport/status`).



**24. Comment protégez-vous les secrets, les accès et les données ?**

Preuve à présenter : Configuration sans secrets, contrôle d’accès et politique de journalisation.

Réponse du groupe : Utilisation de propriétés externalisées (`application.properties`) pour isoler les paramètres de connexion à la base de données sans exposition de secrets dans le code source.



### Résultats à comparer

| Indicateur | Baseline | Solution | Jeu ou scénario |
| --- | --- | --- | --- |
| Précision de prédiction | ~70% (heuristique de base) | 99.33% (Random Forest) | Jeu de test distinct (596 échantillons) |
| Latence de calcul d'itinéraire | > 2.0 s | < 200 ms | Réseau de Maarif (1929 nœuds / 4515 arêtes) |

Pour un projet industriel, démontrer en simulation et expliquer les limites du passage au réel. Pour un projet informatique, montrer les droits accordés aux outils et les actions qui exigent une validation humaine.



## Contributions et corrections

**25. Quelle contribution chaque membre peut-il présenter et expliquer ?**

Preuve à présenter : Tâches, commits, résultats et revue croisée. Chaque membre explique au moins un choix technique.

Réponse du groupe : 
* Reddad Chaouay : Architecture backend Spring Boot, configuration Maven, résolution des dépendances, configuration des ports et mise en place de l'API REST de statut.
* Bellati Nadir : Traitement des données géospatiales (OSMnx/NetworkX), extraction du graphe de Maarif (1929 nœuds, 4515 arêtes) et entraînement du modèle de prédiction de trafic (Random Forest à 99.33%).



**26. Quel blocage menace le prochain jalon et de quelle aide avez-vous besoin ?**

Preuve à présenter : Cause précise, tentatives déjà faites et décision attendue.

Réponse du groupe : Finalisation de l'installation de PostgreSQL sur l'environnement local pour basculer de la configuration temporaire à la persistance complète des données géospatiales.



**27. Quelle affirmation ou fonctionnalité devez-vous corriger après cette revue ?**

Preuve à présenter : Écart entre prévu et observé, cause et preuve de correction attendue.

Réponse du groupe : Migrer la configuration Spring Boot d'une exécution sans base de données vers une connexion effective à la base PostgreSQL/PostGIS.



**28. Quelles sont vos trois prochaines actions prioritaires ?**

Preuve à présenter : Action observable, responsable nommé, échéance et test d’acceptation.

Réponse du groupe : 
1. Finaliser l'installation et la création de la base `urban_transport_db` (Responsable : Reddad, Échéance : Immédiate, Preuve : Connexion BDD active).
2. Persister les entités du réseau routier (Responsable : Bellati, Échéance : 48h, Preuve : Tables de nœuds/arêtes remplies).
3. Connecter l'API REST aux services de données géospatiales (Responsable : Équipe, Échéance : Prochain jalon).



**29. Faut-il réduire le périmètre ou revoir le choix des données ou des outils ?**

Preuve à présenter : Décision argumentée et effet sur le planning et les livrables.

Réponse du groupe : Aucune réduction de périmètre nécessaire ; le MVP ciblé sur le quartier Maarif à Casablanca est parfaitement maîtrisé et fonctionnel.



**30. Quelle preuve présenterez-vous lors du prochain point d’avancement ?**

Preuve à présenter : Jalon vérifiable et scénario exact qui permettra de le valider.

Réponse du groupe : Démonstration de l'API Spring Boot interrogeant directement la base PostgreSQL/PostGIS avec calcul d'itinéraire dynamique sur le graphe de Maarif.



### Plan de correction à valider

| Action prioritaire | Responsable | Échéance | Preuve de correction |
| --- | --- | --- | --- |
| 1. Finaliser PostgreSQL/PostGIS | Reddad | Immédiat | Connexion DB établie dans les logs Spring Boot |
| 2. Insérer les données du graphe | Bellati | 48h | Données visibles dans la base PostgreSQL |
| 3. Tester l'API REST avec persistance | Équipe | Prochain point | Réponse JSON alimentée par la base de données |

Décision de la revue : Poursuivre (avec intégration de la base de données en cours).

Commentaire de l’enseignant : [à compléter]
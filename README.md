# Internship-Management-System

# Table of Contents

- [Technologies](#technologies)
- [Dossier d'analyse](#dossier-danalyse)
  - [Partie donnnées](#partie-donnnées)
    - [Dictionnaire de donnnées](#dictionnaire-de-donnnées)
    - [Règles de gestion](#règles-de-gestion)
    - [MCD: Modèle Conceptuel de Données](#mcd-modèle-conceptuel-de-données)
  - [Partie traitements](#partie-traitements)
    - [Acteurs](#acteurs)
    - [Traitement des stages](#traitement-des-stages)
      - [GOE : Graphe d'Ordonnancement des Événements](#goe---internships-event-scheduling-graph)
      - [MCT : Modèle Conceptuel des Traitements]
      - [MOT : Modèle Organisationnel des Traitements]


# Technologies

**Development Platform:**  
Oracle APEX <img alt="Oracle" width="30px" style="padding-right:10px;" src="https://cdn.jsdelivr.net/gh/devicons/devicon@latest/icons/oracle/oracle-original.svg" />  
(Oracle Application Express - version 23.1)

**Database:**  
Oracle Database 19c <img alt="Oracle" width="30px" style="padding-right:10px;" src="https://cdn.jsdelivr.net/gh/devicons/devicon@latest/icons/oracle/oracle-original.svg" />  
with PL/SQL <img alt="Plsql" width="30px" style="padding-right:10px;" src="https://cdn.jsdelivr.net/gh/devicons/devicon@latest/icons/plsql/plsql-original.svg" />

**Additional Tools:**  
- SQL Developer <img alt="SQL" width="30px" style="padding-right:10px;" src="https://cdn.jsdelivr.net/gh/devicons/devicon@latest/icons/oracle/oracle-original.svg" /> (for database development and administration)

- # Dossier d'analyse

Afin d’assurer une structuration optimale de nos données au sein de notre base, il est nécessaire de suivre une méthodologie de conception rigoureuse. Notre choix s’est porté sur la méthode MERISE (Méthode d’Etude et de Réalisation Informatique pour les Systèmes d’Entreprise), motivé par la clarté et la simplicité de ses modèles, ainsi que par sa capacité à séparer de manière distincte les données des traitements.

## Partie donnnées

### Dictionnaire de donnnées

Le dictionnaire de données est un document qui regroupe et décrit toutes les informations utilisées dans le système. Il précise les entités, les attributs, les types de données, leurs longueurs et leurs significations. Chaque donnée a des règles de validation et de saisie pour assurer sa précision et sa pertinence. Il nous aide à mieux comprendre la structure des données.

| Entity        | Attribute          | Type         | Description                          | Constraints          |
|---------------|--------------------|--------------|--------------------------------------|----------------------|
| **Offre**     | tuteur             | VARCHAR(50)  | Encadrant externe de l'entreprise assigné avec l'offre | Non nul |
| **Utilisateur** | id_user           | ID           | Identifiant unique de l'utilisateur  | Clé primaire, auto-incrément |
|               | login_user         | VARCHAR(50)  | Identifiant unique utilisé pour la connexion au système | Unique, non nul |
|               | mdps_user          | VARCHAR(100) | Mot de passe hashé de l'utilisateur  | Non nul |
|               | type_user          | ENUM         | Type d'utilisateur (Etudiant, entreprise, gestionnaires) | Non nul |
| **Candidature** | id_cand           | ID           | Identifiant unique de la candidature | Clé primaire |
|               | id_etudiant        | ID           | Référence à l'étudiant               | Clé étrangère |
|               | id_offre           | ID           | Référence à l'offre de l'étudiant    | Clé étrangère |
|               | statut_cand        | ENUM         | Statut de la candidature (En attente, Acceptée, Refusée) | Par défaut: En attente |
|               | date_cand          | DATE         | Date de soumission                   | Générée automatiquement |
| **Gestionnaire** | id_gest           | ID           | Identifiant du gestionnaire           | Clé primaire |
|               | nom_gest           | VARCHAR(50)  | Nom du gestionnaire                   | Non nul |
|               | prenom_gest        | VARCHAR(50)  | Prénom du gestionnaire                | Non nul |
|               | email_gest         | VARCHAR(100) | Adresse email du gestionnaire         | Unique, non nul |
|               | tel_gest           | VARCHAR(15)  | Numéro de téléphone                   | Non nul |
|               | promotion_associee | ENUM         | Promotion supervisée (1A, 2A ou 3A ou tous) | Non nul (null si gestionnaire de l'école) |
| **Convention** | id_convention      | ID           | Identifiant unique de la convention   | Clé primaire |
|               | id_cand            | ID           | Référence à la candidature validée    | Clé étrangère |
|               | statut_convention  | ENUM         | Statut de la convention (En cours, signée, refusée) | Par défaut: En cours |
|               | fichier_pdf        | BLOB         | Convention générée au format PDF      | Non nul |

### Règles de gestion
Les règles de gestion définissent les contraintes, les validations et les relations qui encadrent l’utilisation des données dans le système. Elles permettent de garantir l’intégrité et la cohérence des informations, tout en traduisant les exigences métier en mécanismes opérationnels.

#### Gestion des comptes utilisateurs :
 - Un utilisateur peut être un étudiant, une entreprise, un gestionnaire de l’école, ou un gestionnaire de stages.
 - Chaque utilisateur dispose d’un compte unique sur la plateforme.
 - Un compte étudiant ou entreprise doit être validé par le gestionnaire principal avant d’être activé.
 - Un gestionnaire secondaire est créé et attribué par le gestionnaire principal.
 - Une entreprise peut gérer plusieurs offres de stages.

#### Gestion des offres de stages :
 - Une offre de stage est publiée par une entreprise ou par un gestionnaire secondaire.
 - Chaque offre doit être validée par un gestionnaire secondaire avant publication.
 - Une offre de stage est associée à une et une seule entreprise.
 - Une offre de stage peut être consultée par tous les étudiants.
 - Une offre de stage peut être retirée à tout moment par l’entreprise ou le gestionnaire secondaire.

#### Gestion des candidatures :
- Un étudiant peut candidater à plusieurs offres de stages
- Un étudiant peut soumettre une seule candidature pour une même offre de stage
- Un étudiant ne peut postuler qu'aux offres correspondant à sa promotion
- Lorsqu'une candidature est acceptée, l'offre devient indisponible pour les autres étudiants

#### Gestion des stages :
- Un étudiant peut effectuer un ou plusieurs stages durant son cursus
- Chaque stage est associé à un seul étudiant
- Chaque stage est supervisé par un tuteur désigné par l'entreprise
- Un tuteur peut superviser plusieurs stages
- Une convention de stage est générée automatiquement après validation d'une candidature

#### Gestion des validations et droits :
- Les gestionnaires secondaires valident les offres de stages
- Le gestionnaire principal approuve/rejette les comptes utilisateurs (entreprises, étudiants, gestionnaires)
- Les gestionnaires secondaires sont spécialisés par promotion (1A, 2A, 3A)
- Les entreprises n'ont accès qu'à leurs propres offres et stages

### MCD: Modèle Conceptuel de Données
 Le MCD est une représentation graphique des données et des relations entre elles. Il traduit les besoins
métier en un schéma conceptuel qui montre comment les différentes entités interagissent. Dans ce projet, les
principales entités sont :

#### Gestionnaire Principal
- Représente le responsable de l'école (ENSIAS)
- Gère les relations entre étudiants, stages et conventions
- Crée les comptes des gestionnaires de stages
- Approuve les comptes créés par les étudiants et entreprises

#### Gestionnaire Secondaire
- Responsable de la coordination et du suivi des stages
- Répartition par niveaux académiques :
  - 1ère année (1A)
  - 2ème année (2A) 
  - 3ème année (3A)

#### Utilisateur
- Profils possibles :
  - Étudiant
  - Gestionnaire
  - Entreprise

#### Étudiant
- Soumet des candidatures pour des offres de stage
- Gère son profil et ses documents

#### Entreprise 
- Propose des offres de stage
- Consulte les candidatures reçues

#### Offre
- Proposition de stage créée par une entreprise
- Contient les détails du stage proposé

#### Candidature
- Demande d'un étudiant pour une offre spécifique
- Contient toutes les informations relatives à la candidature

#### Convention
- Document généré après acceptation mutuelle
- Formalise l'accord de stage

#### Relations Principales

- **Soumet** : Étudiant → Candidature → Offre
- **Propose** : Entreprise → Offre
- **Valide/Rejette** : Gestionnaire → Candidature
- **Génère** : Système → Convention (après acceptation)
- **Approuve** : Gestionnaire → Compte Utilisateur
- **Est** : (Gestionnaire/Étudiant) → Utilisateur
- **Gère** : Gestionnaire → Offres (par niveau)
- **Reçoit** : Entreprise → Candidatures
<p align="center">
  <img src="assets/MCD.png" alt="Le modèle conceptuel de données"/>
</p>

## Partie traitements

Le système que nous avons développé implique plusieurs traitements liés aux étudiants et à leurs stages. Afin de structurer et de clarifier ces processus, nous avons opté pour leur modélisation à travers les diagrammes de traitement de la méthode Merise. Ces traitements se divisent en deux axes principaux : la gestion des inscriptions et la gestion des stages.

### Acteurs

Dans la partie traitement de notre système, plusieurs acteurs jouent un rôle clé pour assurer le bon déroulement des opérations.

#### Étudiant
L'étudiant est le principal utilisateur de la plateforme. Son rôle est de rechercher, postuler et suivre les offres de stage.

**Fonctionnalités :**
- Créer un compte et s'authentifier sur la plateforme
- Compléter son profil (CV, lettre de motivation, coordonnées)
- Consulter les offres de stage disponibles
- Soumettre des candidatures pour les stages
- Suivre l'état des candidatures (en attente, acceptée, rejetée)
- Recevoir des notifications (mises à jour des candidatures, nouvelles offres)

#### Gestionnaire Principal
Responsable de l'administration générale de la plateforme. Valide les comptes des différents utilisateurs.

**Fonctionnalités :**
- Valider ou rejeter les comptes utilisateurs
- Attribuer des responsabilités aux gestionnaires secondaires
- Gérer les conventions de stage générées
- Superviser les activités des gestionnaires secondaires
- Gérer les paramètres généraux de la plateforme

#### Gestionnaire Secondaire
En charge de la supervision des offres et candidatures, et de l'interaction avec les entreprises.

**Fonctionnalités :**
- Superviser les offres de stage soumises
- Publier des recommandations/offres spécifiques par promotion (1A, 2A, 3A)

#### Entreprise
Utilise la plateforme pour proposer des stages et recruter des étudiants.

**Fonctionnalités :**
- Créer un compte et publier des offres de stage
- Consulter et valider/rejeter les candidatures
- Collaborer pour formaliser les conventions de stage

### Traitement des stages

#### GOE : Graphe d'Ordonnancement des Événements

Processus complet de gestion des stages :

1. Lancement de la période des stages à l'ENSIAS
2. Soumission des offres par les entreprises
3. Validation/rejet des offres par les gestionnaires
4. Publication des offres validées
5. Soumission des candidatures par les étudiants
6. Examen des candidatures par les entreprises
   - Si refus : Processus s'arrête pour cette candidature
   - Si accepté : Processus continue
7. Génération et livraison de la convention
8. Signature de la convention par l'entreprise
9. Confirmation du stage
10. Désignation d'un professeur encadrant
11. Déroulement du stage avec suivi régulier
12. Remplissage de la fiche d'évaluation
13. Fin du stage

<p align="center">
  <img src="assets/GOE_stages.png" alt="Le graphe d’ordonnancement des événements pour les stages" width="400px"/>
</p>

#### MCT : Modèle Conceptuel des Traitements

Le Modèle Conceptuel des Traitements est une représentation visuelle décrivant le fonctionnement du processus, montrant les différentes étapes et leurs relations. Il illustre la circulation et le traitement des informations dans le système.

**Processus décrit par le MCT :**

1. Début de la période de stage à l'ENSIAS
2. Soumission des offres de stage par les entreprises via la plateforme
3. Validation/rejet des offres par le gestionnaire de stages
4. Publication des offres validées (accessibles aux étudiants)
5. Consultation et candidature des étudiants aux offres disponibles
6. Examen des candidatures par l'entreprise :
   - **Acceptation** :
     - Confirmation du stage
     - Génération de la convention
     - Signature par l'entreprise
     - Affectation d'un encadrant interne ENSIAS
     - Début du stage
   - **Rejet** :
     - Notification envoyée à l'étudiant
7. Remplissage de la fiche d'évaluation en fin de stage
8. Mise à jour du statut du stage (terminé)
9. Clôture du processus de stage

<p align="center">
  <img src="assets/MCT - Gestion des inscriptions.png" alt="Le modèle conceptuel des traitements pour les stages"/>
</p>

#### MOT : Modèle Organisationnel des Traitements

Le MOT définit la gestion des informations et processus au sein du système, précisant les interactions entre entités et le traitement des données.

**Caractéristiques principales :**

**Entités impliquées :**
- Étudiant
- Gestionnaire de stages (secondaire)
- Entreprise 
- Gestionnaire de l'école (principal)

**Éléments clés :**
1. **Case temps** : Spécifie les étapes du processus :
   - Période de stage
   - Début de stage
   - Fin de stage

2. **Case nature** : Indique le type de traitement :
   - Manuel
   - Automatique
   - Semi-automatique

**Particularités :**
- La plupart des traitements sont manuels
- Exception : Le traitement des réponses aux candidatures est automatique
  - Acceptation d'une candidature → Annulation automatique des autres candidatures de l'étudiant
  - Rejet d'une candidature → Notification à l'étudiant, autres candidatures restent en "attente"
  
<p align="center">
  <img src="assets/MCT - Gestion des inscriptions.png" alt="Le modèle conceptuel des traitements pour les stages"/>
</p>

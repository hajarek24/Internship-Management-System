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
    - [MCC: Modèle Conceptuel des Communications](#mcc-modèle-conceptuel-des-communications)
    - [Traitement des stages](#traitement-des-stages)
      - [GOE - stages: Graphe d’Ordonnancement des Evénements](#goe---stages-graphe-dordonnancement-des-evénements)
      - [MCT - stages: Modèle Conceptuel des Traitements](#mct---stages-modèle-conceptuel-des-traitements)
      - [MOT - stages: Modèle Organisationnel des Traitements](#mot---stages-modèle-organisationnel-des-traitements)
    - [Traitement des inscriptions](#traitement-des-inscriptions)
      - [GOE - inscriptions](#goe---inscriptions)
      - [MCT - inscriptions](#mct---inscriptions)
      - [MOT - inscriptions](#mot---inscriptions)

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

<p align="center">
  <img src="assets/dictionnaire de donnees.PNG" alt="Dictionnaire de données"/>
</p>

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

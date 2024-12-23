 # SAE : Application Web de Service Vélib'

 # Livrable 2 :Développement d'un référentiel

 # Sommaire

1. [Description du Livrable](#description-du-livrable)
2. [Prérequis](#prérequis)
   2.1 [Logiciels nécessaires](#logiciels-nécessaires)
   2.2 [Installation des logiciels](#installation-des-logiciels)
3. [Étapes de Configuration](#étapes-de-configuration)
   3.1 [Créer la base de données dans PostgreSQL](#créer-la-base-de-données-dans-postgresql)
   3.2 [Créer les tables dans PostgreSQL](#créer-les-tables-dans-postgresql)
4. [Gestion des Utilisateurs et des Privilèges](#gestion-des-utilisateurs-et-des-privilèges)
5. [Interagir avec l'API et la Base de Données](#interagir-avec-lapi-et-la-base-de-données)
   5.1 [Récupérer les données via l'API](#récupérer-les-données-via-lapi)
   5.2 [Importer les données dans la base de données](#importer-les-données-dans-la-base-de-données)
6. [Test de l'Application](#test-de-lapplication)
7. [Conclusion](#conclusion)


# 1. Description du projet
Ce livrable présente la conception d'une base de données essentielle au bon fonctionnement du projet Vélib'. Elle est conçue pour centraliser et organiser toutes les informations nécessaires à la gestion des utilisateurs, des stations de vélos, des réservations et des historiques de recherches.

La base de données stocke des données clés telles que les informations des utilisateurs, leurs recherches sur les stations de vélos, ainsi que leurs réservations passées. Elle est structurée de manière à permettre un accès rapide et sécurisé à ces informations.

En résumé, cette base de données est le pilier central du projet Vélib', garantissant une gestion efficace des données et offrant ainsi une expérience utilisateur optimale, fluide et personnalisée, indispensable au bon fonctionnement du site Vélib'.
 

# 2. Prérequis
## 2.1 [Logiciels nécessaires](#logiciels-nécessaires)

- **Python 3.8+** doit être installé.
- Bibliothèques Python nécessaires :
  - `mysql-connector-python` pour la connexion à notre base de données.
  - `requests` pour les requêtes HTTP.
- **MySQL** doit être installé.


## 2.2 [Installation des logiciels](#installation-des-logiciels)

### 2.2.1 [Installation des bibliothèques Python](#installation-des-bibliothèques-python)

Pour installer les bibliothèques nécessaires, exécutez la commande suivante dans votre terminal ou dans le terminal intégré de Visual Studio :

```bash
pip install requests mysql-connector-python
```

### 2.2.2 [Installation de MySQL](#installation-de-mysql)

Installez `mysql-server` avec la commande suivante :

```bash
sudo apt install mysql-server
```
- Après l'installation, si MySQL ne démarre pas automatiquement, vous pouvez le démarrer manuellement en exécutant la commande suivante :

     ```bash
     sudo systemctl start mysql
     ```

### 2.2.3 [Configuration de mysql](#Configuration-de-mysql)

   Pour accéder à MySQL, si on ne vous a pas demandé de définir un mot de passe pour l'utilisateur root, commencez par taper la commande suivante pour en définir un :

   ```bash
   sudo mysql -u root
   ```

   Après avoir accédé à MySQL, pour définir un mot de passe, tapez la commande suivante :

   ```bash
   ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'new_password';
   ```

   Maintenant, le mot de passe est bien défini. Pour accéder à la base de données en tant que root, tapez la commande suivante :

   ```bash
   sudo mysql -u root -p
   ```

   Il vous sera alors demandé de saisir le mot de passe que vous venez de définir.


# 3. [Étapes de Configuration](#étapes-de-configuration)
   
## 3.1 [Créer la base de données dans PostgreSQL](#créer-la-base-de-données-dans-postgresql)

Avant de pouvoir commencer à utiliser PostgreSQL dans votre projet, il est essentiel de créer une base de données. Suivez les étapes ci-dessous pour créer une nouvelle base de données :

1. **Accédez à PostgreSQL** :
   Ouvrez un terminal et connectez-vous à PostgreSQL avec l'utilisateur `postgres` (ou un autre utilisateur si vous avez configuré un autre utilisateur pour PostgreSQL) :

   ```bash
   sudo -u postgres psql  
   ``` 

   Une fois connecté à PostgreSQL, vous pouvez créer une nouvelle base de données en exécutant la commande suivante :

   ```sql
   CREATE DATABASE sae_velib;
   ```

## 3.2 [Créer les tables dans PostgreSQL](#Créer-les-tables-dans-PostgreSQL)

Une fois la base de données `sae_velib` créée, nous pouvons procéder à la création des tables qui stockeront les données nécessaires au bon fonctionnement du projet. Pour cela, suivez les étapes ci-dessous :

 **Se connecter à PostgreSQL** :  
   Connectez-vous à PostgreSQL en utilisant la commande suivante et sélectionnez la base de données `sae_velib` :

   ```bash
   psql -U postgres -d sae_velib
   ```
   ##  Tables principales
### 1. Table users
Cette table gère les informations des utilisateurs.

**Colonnes principales :**
- `id` : Identifiant unique de l'utilisateur.
- `username` : Nom d'utilisateur.
- `email` : Adresse email de l'utilisateur.
- `password` : Mot de passe de l'utilisateur (stocké sous forme hachée pour plus de sécurité).
- `created_at` : Date et heure de création du compte.

### 2. Table velo
Cette table contient les informations sur les types de vélos.

**Colonnes principales :**
- `id_velo` : Identifiant unique du type.
- `type` : Type de vélo (électrique, classique).

### 3. Table stations
Cette table regroupe les informations de toutes les stations de vélos disponibles chez Vélib'.

**Colonnes principales :**
- `station_id` : Identifiant unique de la station.
- `lat` : Latitude de la station.
- `lon` : Longitude de la station.
- `station` : Nom de la station.

### 4. Table reservations
Cette table stocke les réservations effectuées par les utilisateurs.

**Colonnes principales :**
- `id` : Identifiant unique de la réservation.
- `confirmationID` : Code de confirmation unique pour la réservation.
- `id_velo` : Référence au vélo réservé (liée à `velo.id_velo`).
- `client_id` : Référence à l'utilisateur ayant effectué la réservation (liée à `users.id`).
- `create_time` : Date et heure de la réservation.
- `station_id` : Référence à la station de réservation (liée à `stations.station_id`).


### 5. Table recherches
Cette table stocke les recherches effectuées par les utilisateurs.

**Colonnes principales :**
- `id` : Identifiant unique de la recherche.
- `client_id` : Référence à l'utilisateur ayant effectué la recherche (liée à `users.id`).
- `recherche` : Terme recherché (elle est `NULL` si le terme recherché est une station existante dans la table `stations`).
- `created_at` : Date et heure de la recherche.
- `resultat` : Résultat de la recherche (succès ou échec).
- `station_id` : Référence à une station liée à la recherche (liée à `stations.station_id`) (elle est `NULL` si le terme recherché est une station non existante dans la table `stations`).
  

**Relations :**
Clés étrangères pour relier cette table à :
- `client_id → users.id`
- `station_id → stations.station_id`


##  Création des vues

Les vues permettent de simplifier les requêtes et de présenter les données sous une forme plus lisible en combinant plusieurs tables.

##  Vue `recherches_vue`

Cette vue affiche l'historique des recherches effectuées par les utilisateurs, avec des informations supplémentaires sur les stations associées.  
Elle utilise un `LEFT JOIN` pour inclure toutes les recherches, même celles qui ne sont pas liées à une station valide.


 ##  Vue `reservations_vue`

La vue `reservations_vue` offre un récapitulatif détaillé des réservations effectuées par les utilisateurs.  
Elle combine les données des tables suivantes :
- **`reservations`** : Détails des réservations, y compris l'utilisateur et le code de confirmation.
- **`stations`** : Informations sur la station de départ ou d'arrivée.
- **`velo`** : Type de vélo réservé (électrique ou mecanique).

## 5. Diagramme UML

Le diagramme UML (Unified Modeling Language) ci-dessous illustre la structure du système et la manière dont les différentes entités interagissent les unes avec les autres dans le cadre du projet Vélib'. Il représente les relations entre les principales entités, telles que les utilisateurs, les vélos, les stations et les réservations. 

Ce diagramme permet de mieux comprendre l'architecture du système, facilitant ainsi la maintenance et l'évolution du projet. Vous y trouverez également une vue d'ensemble des clés primaires et des relations entre les différentes tables de la base de données.

### Structure du Diagramme
- **Entités** : Utilisateurs, Vélos, Stations, Réservations, etc.
- **Relations** : Liaisons entre les entités, comme les réservations effectuées par les utilisateurs ou les vélos réservés dans une station.

Vous pouvez consulter le diagramme ci-dessous pour obtenir un aperçu visuel des relations et des entités du projet.

![Diagramme UML de la structure du projet Vélib'](C:\Users\ZIOTECH\Pictures\Screenshots)

**_Le fichier contenant la création des tables et des vues est intitulé : `Velib_SAE_Tableau.sql`_**


## 4. Gestion des Utilisateurs et des Privilèges

La gestion des utilisateurs et des privilèges permet de contrôler l'accès aux données de la base de données et de s'assurer que les utilisateurs peuvent seulement effectuer des actions autorisées. Pour ce projet Vélib', un utilisateur spécifique a été créé avec des privilèges limités sur certaines tables et vues. 

### 4.1 Création de l'Utilisateur

L'utilisateur `user_velib` est créé avec le mot de passe `saevelib`. Voici la commande SQL pour créer cet utilisateur dans MySQL ou PostgreSQL :

```sql
-- Créer un utilisateur nommé 'user_velib' avec un mot de passe 'saevelib'
CREATE USER 'user_velib'@'localhost' IDENTIFIED BY 'saevelib';
```

## Attribution des privilèges sur la table `stations`

Dans cette étape, nous allons attribuer des privilèges **`SELECT`** à l'utilisateur `user_velib` sur la table `stations`. Cela permettra à cet utilisateur de lire les données présentes dans la table des stations, mais pas de les modifier.

### Commande pour attribuer les privilèges

```sql
-- privilèges sur la table stations (uniquement SELECT)
GRANT SELECT ON sae_velib.stations TO 'user_velib'@'localhost';
```

##  Attribution des privilèges sur la table `velo`

Dans cette étape, nous allons attribuer des privilèges **`SELECT`** à l'utilisateur `user_velib` sur la table `velo`. Cela permettra à cet utilisateur de lire les informations concernant les types de vélos disponibles, mais pas de les modifier.

### Commande pour attribuer les privilèges

```sql
-- privilèges sur la table velo (uniquement SELECT)
GRANT SELECT ON sae_velib.velo TO 'user_velib'@'localhost';
```

##  Attribution des privilèges sur la table `reservations`

Dans cette étape, nous allons attribuer des privilèges **`SELECT`** et **`INSERT`** à l'utilisateur **`user_velib`** sur la table **`reservations`**. Ces privilèges permettent à l'utilisateur de lire et d'ajouter des réservations dans la base de données, mais sans pouvoir les modifier ou les supprimer.

### Commande pour attribuer les privilèges

```sql
-- privilèges sur la table reservations (SELECT, INSERT)
GRANT SELECT, INSERT ON sae_velib.reservations TO 'user_velib'@'localhost';
```

##  Attribution des privilèges sur la table `recherches`

Dans cette étape, nous allons attribuer des privilèges **`SELECT`**, **`DELETE`** et **`INSERT`** à l'utilisateur **`user_velib`** sur la table **`recherches`**. Ces privilèges permettent à l'utilisateur de lire, supprimer et ajouter des enregistrements dans la table des recherches effectuées par les utilisateurs.

### Commande pour attribuer les privilèges

```sql
-- privilèges sur la table recherches (SELECT, DELETE, INSERT)
GRANT SELECT, DELETE, INSERT ON sae_velib.recherches TO 'user_velib'@'localhost';
```

##  Attribution des privilèges sur la table `users`

Dans cette étape, nous allons attribuer des privilèges **`SELECT`**, **`UPDATE`** et **`INSERT`** à l'utilisateur **`user_velib`** sur la table **`users`**. Ces privilèges permettent à l'utilisateur de lire, modifier et ajouter des informations sur les utilisateurs du système.

### Commande pour attribuer les privilèges

```sql
-- privilèges sur la table users (SELECT, UPDATE, INSERT)
GRANT SELECT, UPDATE, INSERT ON sae_velib.users TO 'user_velib'@'localhost';
```

##  Attribution des privilèges sur la vue `recherches_vue`

Dans cette étape, nous allons attribuer le privilège **`SELECT`** à l'utilisateur **`user_velib`** sur la vue **`recherches_vue`**. Ce privilège permet à l'utilisateur de consulter les résultats des recherches effectuées par les utilisateurs dans l'application Vélib'.

### Commande pour attribuer le privilège

```sql
-- privilèges sur la vue recherches_vue (uniquement SELECT)
GRANT SELECT ON sae_velib.recherches_vue TO 'user_velib'@'localhost';
```

##  Attribution des privilèges sur la vue `reservations_vue`

Dans cette étape, nous allons attribuer le privilège **`SELECT`** à l'utilisateur **`user_velib`** sur la vue **`reservations_vue`**. Ce privilège permet à l'utilisateur de consulter les informations liées aux réservations effectuées par les utilisateurs dans l'application Vélib'.

### Commande pour attribuer le privilège

```sql
-- privilèges sur la vue reservations_vue (uniquement SELECT)
GRANT SELECT ON sae_velib.reservations_vue TO 'user_velib'@'localhost';
```

- **`FLUSH PRIVILEGES`** :
  - Cette commande est utilisée pour appliquer immédiatement les modifications de privilèges effectuées. Elle permet de recharger les privilèges et de les rendre actifs sans avoir besoin de redémarrer le serveur MySQL.


  Dans cette section, nous avons configuré les privilèges d'accès pour l'utilisateur user_velib sur différentes tables et vues de la base de données sae_velib. Ces étapes garantissent que chaque utilisateur dispose des droits appropriés en fonction de ses besoins, tout en préservant la sécurité et l'intégrité des données.

  **_Le fichier contenant la création de l'utilisateur : `Velib_SAE_USER.sql`_**


## 5. Interagir avec l'API et la Base de Données
Cette section explique les étapes nécessaires pour interagir avec l'API Vélib afin de récupérer les données des stations et les importer dans la base de données.

## 5.1 Récupérer les données via l'API
Afin d’obtenir les informations des stations depuis l'API, nous devons faire une requête HTTP pour récupérer les données sous format JSON. Ces données incluent des informations essentielles telles que l'ID de la station, sa latitude, sa longitude et son nom.

Le processus est le suivant :
# 1. Définir l'URL de l'API
L'API utilisée pour récupérer les données des stations Vélib est une API locale Flask. L'URL à utiliser pour récupérer les données des stations est :
```python
url_api = "http://localhost:5000/stations"
```
Cette URL permet de récupérer toutes les informations des stations depuis l'API.

# 2. Envoyer la requête GET pour récupérer les données
Nous envoyons ensuite une requête HTTP GET à cette API pour obtenir les données sous format JSON.
```python
import requests

response = requests.get(url_api)
stations = response.json()
```
requests.get(url_api) : Envoie la requête HTTP GET à l'API.
response.json() : Convertit la réponse de l'API (au format JSON) en un objet Python. Ici, nous obtenons une liste de stations.

# 3. Vérification des données
Les données des stations sont maintenant sous forme de liste de dictionnaires, chacun représentant une station avec des informations telles que l'ID de la station, la latitude, la longitude, et le nom de la station. Voici un exemple de structure de données :
```json
[
  {
    "station_id": 1,
    "lat": 48.8566,
    "lon": 2.3522,
    "name": "Station 1"
  },
  {
    "station_id": 2,
    "lat": 48.8577,
    "lon": 2.3533,
    "name": "Station 2"
  }
]
```
## 5.2 Importer les données dans la base de données
Une fois les données récupérées via l'API, l’étape suivante consiste à importer ces informations dans la base de données MySQL. Le script Python suivant vous guide pour insérer ou mettre à jour les stations dans la base de données.
# 1. Connexion à la base de données
Avant de pouvoir importer les données, il est nécessaire de se connecter à la base de données MySQL.

```python
import mysql.connector

conn = mysql.connector.connect(
    host="localhost",  # Remplacez par votre hôte
    user="root",  # Remplacez par votre utilisateur de base de données
    password="yannel",  # Remplacez par votre mot de passe
    database="user"  # Remplacez par le nom de votre base de données
)
cursor = conn.cursor(dictionary=True)
```
conn : Objet de connexion à la base de données.
cursor : Objet permettant d’exécuter des requêtes SQL.

# 2. Vérifier si la station existe déjà dans la base de données
Avant d'insérer ou de mettre à jour les stations, nous devons vérifier si elles existent déjà dans la table stations.
```python
for station in stations:
    station_id = station['station_id']
    lat = station['lat']
    lon = station['lon']
    name = station['name']
    
    cursor.execute("SELECT COUNT(*) FROM stations WHERE station_id = %s", (station_id,))
    result = cursor.fetchone()
```
Cette requête vérifie si une station avec le même station_id existe déjà dans la base de données.

# 3. Insérer les nouvelles stations
Si la station n'existe pas encore dans la base de données, nous insérons les données de la station.
```python
if result['COUNT(*)'] == 0:
    try:
        cursor.execute(
            "INSERT INTO stations (station_id, lat, lon, station) VALUES (%s, %s, %s, %s)",
            (station_id, lat, lon, name)
        )
        nbInsertion += 1
    except mysql.connector.IntegrityError as e:
        print(f"Erreur d'insertion pour la station ID {station_id}: {e}")
```
INSERT INTO : Permet d’ajouter une nouvelle station dans la table stations.
nbInsertion : Compteur pour suivre le nombre d’insertion.

# 4. Mettre à jour les stations existantes
Si la station existe déjà, nous comparons ses informations actuelles avec celles de l'API. Si des différences sont trouvées (par exemple, une différence de coordonnées ou de nom), nous mettons à jour la station.
```python
else:
    cursor.execute("SELECT lat, lon, station FROM stations WHERE station_id = %s", (station_id,))
    donner = cursor.fetchone()

    current_lat = donner['lat']
    current_lon = donner['lon']
    current_name = donner['station']
    
    if current_lat != lat or current_lon != lon or current_name != name:
        try:
            cursor.execute(
                "UPDATE stations SET lat = %s, lon = %s, station = %s WHERE station_id = %s",
                (lat, lon, name, station_id)
            )
            nbUpdate += 1
        except mysql.connector.Error as e:
            print(f"Erreur de mise à jour pour la station ID {station_id}: {e}")
```
UPDATE : Mise à jour des informations de la station si nécessaire.
nbUpdate : Compteur pour suivre le nombre de mises à jour effectuées.

# 5. Validation des modifications
Une fois les données insérées ou mises à jour, il est nécessaire de valider les modifications dans la base de données.
```python
conn.commit()
```
conn.commit() : Applique les changements effectués dans la base de données.

# 6. Fermeture de la connexion à la base de données
Une fois que tout est inséré et mis à jour, nous fermons la connexion à la base de données pour libérer les ressources.
```python
cursor.close()
conn.close()
```

# 7. Exécution automatique du script
Il est conseillé de programmer l'exécution régulière de ce script, par exemple toutes les 1 à 2 heures, afin de garantir que les données des stations restent toujours à jour. Vous pouvez automatiser l'exécution en utilisant un planificateur de tâches comme cron sur Linux.

**_Le fichier contenant le scripte d'insertion : `Insertion.py`_**




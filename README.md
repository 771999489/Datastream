<h1><b><i> Project_name : Datastream with python, RabbitMQ et MySQL </i></b></h1>

<h2> Objectif : </h2>
L’objectif du projet est de créer un système d'analyse des logs d'un serveur web en temps réel en utilisant Python, RabbitMQ et MySQL. Le système comprend un Producer appelé logs-producer qui lit ligne par ligne un fichier de logs web-server-nginx.log, les publie dans un exchange de type topic, et les envoie à deux files d'attente, l'une appelée queue-data-lake et l'autre queue-data-clean. Le système comprend également deux consumers, le data-lake-consumer et le data-clean-consumer, qui traitent chaque file d'attente différemment en temps réel.

<h2> Architechture : </h2>

![alt text](https://github.com/771999489/Datastream/blob/main/mes%20images/architecture.jpg)



<h2> En résumé : </h2>
•	Le logs-producer : lit le fichier de logs web-server-nginx.log, puis publie chaque ligne de log dans un échange de type topic en utilisant une clé de routage "logs".     L'échange est lié à deux files d'attente, queue-data-lake et queue-data-clean. Chaque file d'attente est consommée par un consumer différent. </p>
•	Le data-lake-consumer se connecte à une base de données MySQL et insère chaque ligne de log en tant qu'un enregistrement dans la table raw-log.  </p>
•	Le data-clean-consumer nettoie et formate chaque ligne de log, puis l'insère en tant qu'un enregistrement dans la table clean-log.</p>
![alt text](https://github.com/771999489/Datastream/blob/main/mes%20images/images%20rabbitMQ.JPG)
<h2>Travail fait : </h2>
<h2>1.	Environnement virtuel</h2>
J’ai travaillé dans un environnement virtuel qui est un mécanisme qui permet de séparer les dépendances requises par différents projets en créant des environnements virtuels qui sont isolés entre eux.</p>
J’ai créé l’instance de RABBITMQ avec l'aide de docker-composer et le fichie. .env nous allons exécuter la commande suivante</p>
Le script .env :</p>
Définit des variables d'environnement pour différents identifiants et mots de passe de bases de données et d'hôtes.</p>
 	RABBIT_USER: Définit le nom d'utilisateur du serveur RabbitMQ (un serveur de messagerie open-source). </p>
 	RABBIT_PASSWORD: Définit le mot de passe associé à l'utilisateur RabbitMQ.</p>
 	MYSQL_ROOT_PASSWORD: Définit le mot de passe de l'utilisateur root de la base de données MySQL. Dans ce cas, il est défini comme "root".</p>
 	MYSQL_DATABASE: Définit le nom de la base de données MySQL à utiliser. </p>
 	MYSQL_USER: Définit le nom d'utilisateur à utiliser pour se connecter à la base de données MySQL. </p>
 	MYSQL_PASSWORD: Définit le mot de passe associé à l'utilisateur MySQL.</p>
 	HOST: Définit l'adresse de l'hôte où se trouve la base de données.</p>
 	PORT: Définit le port à utiliser pour se connecter à la base de données.</p>
Le script docker-compose.yml : Ce fichier est un fichier de configuration pour docker-compose, un outil qui permet de déployer et de gérer des applications en utilisant des conteneurs Docker </p>
Le fichier de configuration crée également un volume nommé RabbitMQ qui sera utilisé pour stocker les données du service RabbitMQ</p>

<h2>2.	LES DIFFERENTS SCRIPTS DU PROJET</h2>
<br>==> Log Producer </br>
1.	Importe les modules pika, mysql.connector et re.

2.	Établit une connexion à une base de données MySQL en utilisant les informations d'identification fournies.
3.	Crée une fonction de rappel pour traiter les messages provenant des files d'attente RabbitMQ. La fonction extrait les données de chaque ligne de journal en utilisant une expression régulière, puis les insère dans une table de la base de données MySQL appelée raw_log.
4.	Établit une connexion avec le serveur RabbitMQ en utilisant les informations d'identification fournies.
5.	Déclare un échange de type topic appelé logs et deux files d'attente appelées queue-data-lake et queue-data-clean.
6.	Lie les files d'attente à l'échange à l'aide de la clé de routage logs.

7.	Lit chaque ligne du fichier de journal web-server-nginx.log et les publie sur l'échange logs avec la clé de routage logs.
8.	Configure un consommateur pour traiter les messages de la file d'attente queue-data-lake, qui appelle la fonction de rappel process_message pour traiter chaque message. Le consommateur démarre ensuite la consommation des messages sur la file d'attente.

<br>==> Data-lake-cosummer</br>
Voici les éléments essentiels du code :
1.	Import de la bibliothèque pika pour se connecter à RabbitMQ et de la bibliothèque mysql.connector pour se connecter à la base de données MySQL.
2.	Ouverture d'une connexion à RabbitMQ en utilisant les identifiants 
3.	Déclaration de la file d'attente queue-data-lake utilisée pour recevoir les messages.
4.	Définition de la fonction clean_log_line qui nettoie les données de chaque message en extrayant l'adresse IP, la date et l'heure, la méthode HTTP, l'URL et le code de statut.
5.	Définition de la fonction callback qui prend en charge chaque message. Elle décode le message en bytes, nettoie les données en appelant clean_log_line, stocke les données nettoyées dans la base de données MySQL, puis envoie une réponse.
6.	Configuration de la consommation de messages en utilisant la fonction basic_consume de RabbitMQ pour recevoir les messages de la file d'attente queue-data-lake et en appelant la fonction callback pour chaque message.
7.	Démarrage de la consommation de messages en utilisant la méthode start_consuming de RabbitMQ.</p>
__NB : que la fonction hashlib.sha256() est utilisée pour calculer le hachage SHA-256 de chaque ligne de journal nettoyée, qui est stockée dans la base de données en tant qu'ID unique pour chaque ligne de journal.__

==> Data-clean-consummer</p>
Ce script Data-clean-consummer est utilisé pour traiter et nettoyer les données de fichiers journaux. Le but est de les transformer en un format facilement exploitable pour une analyse ultérieure.</p>
Le script commence par importer plusieurs modules tels que:</p> 
 	__urllib.parse__ pour la manipulation des URL, </p></p></p></p></p></p>
 	__time__ pour les opérations sur les dates et les heures, </p></p></p></p></p>
 __re__ pour les expressions régulières, </p></p></p></p>
 	__hashlib__ pour le hachage des identifiants et </p></p></p>
 	__DbIpCity__ pour l'obtention des informations sur les adresses IP.</p>
La fonction principale de ce script est process_msg_data_clean(), qui prend en entrée un message brut sous forme de chaîne de caractères. Elle utilise une expression régulière pour extraire les informations du message brut, telles que l'adresse IP, l'heure et la date, la méthode REST, l'URL, le code de statut, la taille de la réponse, etc.</p>
Ensuite, les différentes fonctions sont appelées pour extraire les informations supplémentaires.</p>
 	__get_country()__ utilise l'adresse IP extraite pour obtenir le nom du pays correspondant à cette adresse IP.</p>
 	__get_size_kb()__ et get_size_mb() convertissent la taille de la réponse en kilo-octets et mégaoctets respectivement.</p>
Enfin, une instance de la classe __CleanLog__ est créée pour stocker les données nettoyées. Les données sont stockées dans une base de données à l'aide de SQLAlchemy, ce qui permet une analyse facile et une visualisation des données à l'aide d'outils tels que pandas ou matplotlib.</p>
==> main.py</p>
Là j’ai utilisé SQLAlchemy pour créer une instance de moteur de base de données MySQL, ainsi qu'une session pour interagir avec la base de données.</p> J’ai aussi utilisé la bibliothèque __dotenv__ pour charger les variables d'environnement à partir d'un fichier __.env.__</p>
La fonction __CreateEngine()__ utilise les informations de connexion stockées dans le fichier __.env__ pour créer une URL de connexion à la base de données MySQL.</p> Elle crée ensuite une instance de moteur de base de données en utilisant l'URL de connexion, se connecte au moteur de base de données et crée toutes les tables définies dans le __modèle SQLAlchemy.__</p>
Enfin, elle crée une instance de session en utilisant le moteur de base de données et retourne cette instance de session. Cela permet à l'utilisateur d'interagir avec la base de données en utilisant l'API ORM de SQLAlchemy.</p>
==> models.py</p>
Le code ci-dessus définit deux classes SQLAlchemy - __CleanLog et RowLog__ </p>
 	1. La classe CleanLog est utilisée pour stocker des informations de journal propres et structurées</p>
  2. 	La classe RowLog est utilisée pour stocker des journaux bruts tels qu'ils sont reçus. Chaque classe hérite de la classe Base qui est importée depuis SQLAlchemy.</p>
__Les colonnes de la table CleanLog incluent un identifiant de ligne (id), une horodatage (timestamp), des informations sur la date et l'heure (year, month, day, day_of_week et time), des informations sur l'adresse IP (ip), des informations sur le pays et la ville (country et city), des informations sur la session utilisateur (session et user), des informations sur les demandes REST (is_email, email_domain, rest_method, url, schema, host, rest_session), des informations sur la réponse REST (status, status_verbose), et des informations sur la taille de la réponse (size_bytes, size_kilo_bytes, size_mega_bytes).__</p>

__La table RowLog contient également un identifiant de ligne (id), un horodatage (timestamp) et une colonne log qui contient le journal brut__</p>

<h2> 3.	Insertion dans la base de données</h2><p>
Comme j’ai expliqué ci-dessus, j’ai utilisé une base de données MySQL créée à partir d'un conteneur et un phpMyAdmin pour l'administrer. (Voir docker-compose.yml)</p>

J’ai aussi utilisé un ORMpour interagir avec la base de données et gérer d’une façon correcte les secrets comme users et mot de passe pour MySQL et RabbitMQ</p>

**NB : j’ai mis dans github mes deux fichiers sql : datastream_clean_logs.sql et datastream_raw_log.sql pour voir les résultats obtenus**





<h1><b><i> Project_name : Datastream with python, RabbitMQ et MySQL </i></b></h1>

<h2> Objectif : </h2>
L’objectif du projet est de créer un système d'analyse des logs d'un serveur web en temps réel en utilisant Python, RabbitMQ et MySQL. Le système comprend un Producer appelé logs-producer qui lit ligne par ligne un fichier de logs web-server-nginx.log, les publie dans un exchange de type topic, et les envoie à deux files d'attente, l'une appelée queue-data-lake et l'autre queue-data-clean. Le système comprend également deux consumers, le data-lake-consumer et le data-clean-consumer, qui traitent chaque file d'attente différemment en temps réel.

<h2> Architechture : </h2>

![alt text](https://github.com/771999489/Datastream/blob/main/architecture.jpg)



<h2> En résumé : </h2>
•	Le logs-producer : lit le fichier de logs web-server-nginx.log, puis publie chaque ligne de log dans un échange de type topic en utilisant une clé de routage "logs".     L'échange est lié à deux files d'attente, queue-data-lake et queue-data-clean. Chaque file d'attente est consommée par un consumer différent. </p>
•	Le data-lake-consumer se connecte à une base de données MySQL et insère chaque ligne de log en tant qu'un enregistrement dans la table raw-log.  </p>
•	Le data-clean-consumer nettoie et formate chaque ligne de log, puis l'insère en tant qu'un enregistrement dans la table clean-log.</p>

<h3>Travail fait : </h3>
<h2>1.	Environnement virtuel<h2>
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



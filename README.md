<h1><b><i> Project_name : Datastream with python, RabbitMQ et MySQL </i></b></h1>

<h3> Objectif : </h3>
L’objectif du projet est de créer un système d'analyse des logs d'un serveur web en temps réel en utilisant Python, RabbitMQ et MySQL. Le système comprend un Producer appelé logs-producer qui lit ligne par ligne un fichier de logs web-server-nginx.log, les publie dans un exchange de type topic, et les envoie à deux files d'attente, l'une appelée queue-data-lake et l'autre queue-data-clean. Le système comprend également deux consumers, le data-lake-consumer et le data-clean-consumer, qui traitent chaque file d'attente différemment en temps réel.

<h3> Architechture : </h3>
![alt text](https://github.com/771999489/Datastream/blob/main/architecture.PNG)

![alt text](https://github.com/771999489/mysql-powerBI/blob/main/images/architechture%20du%20projet.JPG)

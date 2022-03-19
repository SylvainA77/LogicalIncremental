# LogicalIncremental
How to create a logical incremental backup

Steps : 

step1 : 
take a logical full backup with GTID coordinates
mariadb-dump --user=<USER> --password=<PASSWORD> --single transaction --master-data --gtid --all-databases > /data/backup/dbs.sql

step2 :
extraire les coordonnées du backup du fichier de backup : filename + position
  
step3 :
prendre les coordonnées actuelle du serveur : filename + position

step4 :
trouver le nom du fichier correspondant 
  
step5 :
creer le fichier sql depuis les logs binaires

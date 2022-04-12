# LogicalIncremental
How to : logical incremental backup

Steps : 

step1 : 
take a logical full backup with replication coordinates
example : 
mariadb-dump --user=<USER> --password=<PASSWORD> --single transaction --master-data --gtid --all-databases > /data/backup/dbs.sql

step2 :
extract backup coordinate from CHANGE MASTER TO command : filename_bkp + position_bkp + gtid_bkp
  
step3 :
get actual server coordinates : filename_end + position_end + gtid_end

step4 :
use mariadb-binlog to generate the sql statements between both coordinates into file 
example : 
mariadb-binlog --start-position=position_bkp filename_bkp .... "filename_end-1" > incremental_file
mariadb-binlog --stop-position=position_end finalenmae_end >> incremental_file
  
step5 :
add CHANGE MASTER TO command with end coordinates
  
Option: GTID
if incremental launched with --gtid, use gtid instead of filename + position. allows for incremental backup to be taken on any (even a different one) node of a async/virtually sync replication cluster or even a maxscale binlog router.

bash pseudo code to find GTID on different backup server :   
  
  for file in `mysql -e -B --raw "show binary logs" | cut -f1` 
do
  filename_begin + position_begin:  mysql -e -B --raw 'show binlog events in "$file"' | grep " GTID gtid_bkp" | cut -f1,2
done
  

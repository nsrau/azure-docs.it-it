
<!--
includes/sql-data-warehouse-backup-retention-policies.md

Latest Freshness check:  2016-05-05 , barbkess.

As of circa 2016-04-22, the following topics might include this include:
articles/sql-data-warehouse/sql-data-warehouse-overview-expectations.md
articles/sql-data-warehouse/sql-data-warehouse-overview-backup-and-restore.md
-->
SQL Data Warehouse esegue gli snapshot di tutti i dati dinamici almeno ogni 8 ore usando gli snapshot di Archiviazione di Azure. Tali snapshot vengono conservati per 7 giorni. È quindi possibile ripristinare i dati tornando indietro almeno a uno dei 21 momenti specifici degli ultimi 7 giorni e arrivando fino all'ora in cui è stato acquisito l'ultimo snapshot.

Prima dell'eliminazione del database, SQL Data Warehouse esegue uno snapshot del database e lo conserva per 7 giorni. In questo caso, non mantiene gli snapshot dal database attivo. Ciò consente di ripristinare un database eliminato al momento dell'eliminazione.

SQL Data Warehouse copia gli snapshot in modo asincrono in un'area geografica di Azure diversa per assicurare una maggiore recuperabilità in caso di errori a livello di area. Se non è consentito accedere al database a causa di un errore in un'area di Azure, è possibile ripristinare il database a uno degli snapshot con ridondanza geografica.

<!---HONumber=AcomDC_0525_2016-->
<properties
   pageTitle="Migrare al database SQL tramite la replica transazionale | Microsoft Azure"
   description="Database SQL di Microsoft Azure, migrazione del database, importazione del database, replica transazionale"
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-migrate"
   ms.date="06/07/2016"
   ms.author="carlrab"/>

# Eseguire la migrazione del database di SQL Server al database SQL di Azure tramite la replica transazionale

> [AZURE.SELECTOR]
- [Migrazione guidata in SSMS](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
- [Esportare in un file BACPAC.](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [Importare da file BACPAC.](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [Replica transazionale](sql-database-cloud-migrate-compatible-using-transactional-replication.md)

Questo articolo illustra come eseguire la migrazione di un database di SQL Server compatibile al database SQL di Azure riducendo al minimo il tempo di inattività grazie alla replica transazionale di SQL Server.

## Comprendere l'architettura della replica transazionale

Quando non è possibile rimuovere il database SQL Server dalla produzione durante la migrazione, è possibile usare la replica transazionale di SQL Server come soluzione di migrazione. Per usare questa soluzione, è necessario configurare il database SQL di Azure come sottoscrittore dell'istanza di SQL Server locale di cui si desidera eseguire la migrazione. Il server di distribuzione locale della replica transazionale sincronizzerà i dati dal database locale da sincronizzare (il server di pubblicazione) mentre continua l'esecuzione di transazioni.

È inoltre possibile usare la replica transazionale per eseguire la migrazione di un subset del database locale. La pubblicazione di cui si esegue la replica nel database SQL di Azure può essere limitata a un subset delle tabelle nel database replicato. Inoltre, per ogni tabella replicata, è possibile limitare i dati a un subset di righe e/o di colonne.

Con la replica transazionale, tutte le modifiche ai dati o agli schemi che si verificano tra il momento in cui inizia la migrazione e quello in cui viene completata vengono visualizzate nel database SQL di Azure.

Quando la sincronizzazione è completata e si è pronti a eseguire la migrazione, è sufficiente modificare la stringa di connessione delle applicazioni in modo che puntino al database SQL di Azure anziché al database locale. Una volta che la replica transazionale completa le eventuali modifiche rimaste nel database locale e tutte le applicazioni puntano al database di Azure, è possibile disinstallare la replica lasciando il database SQL di Azure come sistema di produzione.

 ![Diagramma di SeedCloudTR](./media/sql-database-cloud-migrate/SeedCloudTR.png)

## Requisiti della replica transazionale

La replica transazionale è una tecnologia integrata in SQL Server a partire dalla versione 6.5. Si tratta di una tecnologia sperimentata e consolidata che la maggior parte degli amministratori di database conosce e con cui hanno esperienza. Con l'[anteprima di SQL Server 2016](http://www.microsoft.com/server-cloud/products/sql-server-2016/), è ora possibile configurare il database SQL di Azure come [sottoscrittore di replica transazionale](https://msdn.microsoft.com/library/mt589530.aspx) alla pubblicazione locale. L'esperienza che si ottiene impostando il database da Management Studio è identica alla configurazione di un sottoscrittore di replica transazionale in un server locale. Questo scenario è supportato quando il server di pubblicazione e di distribuzione è almeno una delle seguenti versioni di SQL Server:

 - SQL Server 2016 CTP3 (anteprima) e versioni successive 
 - SQL Server 2014 SP1 CU3 e versioni successive
 - SQL Server 2014 RTM CU10 e versioni successive
 - SQL Server 2012 SP2 CU8 e versioni successive
 - SQL Server 2013 SP3 quando verrà rilasciata


> [AZURE.IMPORTANT] Usare sempre la versione più aggiornata di SQL Server Management Studio per restare sincronizzati con gli aggiornamenti di Microsoft Azure e del database SQL. Le versioni precedenti di SQL Server Management Studio non sono in grado di impostare il database SQL come sottoscrittore. [Aggiornare SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).


## Passaggi successivi

- [Scaricare SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)
- [Scaricare la versione più recente di SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx)
- [Anteprima di SQL Server 2016](http://www.microsoft.com/server-cloud/products/sql-server-2016/)

## Risorse aggiuntive

- [Replica transazionale](https://msdn.microsoft.com/library/mt589530.aspx)
- [Novità della versione 12 del database SQL](sql-database-v12-whats-new.md)
- [Transact-SQL partially or unsupported functions](sql-database-transact-sql-information.md) (Funzionalità di Transact-SQL parzialmente supportate o non supportate)
- [Migrate non-SQL Server databases using SQL Server Migration Assistant (Eseguire la migrazione di database non SQL Server mediante SQL Server Migration Assistant)](http://blogs.msdn.com/b/ssma/)

<!---HONumber=AcomDC_0615_2016-->
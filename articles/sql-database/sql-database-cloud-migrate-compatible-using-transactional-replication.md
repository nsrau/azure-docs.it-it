<properties
   pageTitle="Eseguire la migrazione al database SQL tramite la replica transazionale"
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
   ms.workload="data-management"
   ms.date="03/14/2016"
   ms.author="carlrab"/>

# Eseguire la migrazione del database del server SQL al database SQl tramite la replica transazionale

Quando non è possibile rimuovere il database SQL Server dalla produzione durante la migrazione, è possibile usare la replica transazionale di SQL Server come soluzione di migrazione. Con la replica transazionale, tutte le modifiche ai dati o agli schemi che si verificano tra il momento in cui inizia la migrazione e quello in cui viene completata vengono visualizzate nel database SQL di Azure. Una volta completata la migrazione, è sufficiente modificare la stringa di connessione delle applicazioni in modo che puntino al database SQL di Azure anziché al database locale. Una volta che la replica transazionale completa le eventuali modifiche rimaste nel database locale e tutte le applicazioni puntano al database di Azure, è possibile disinstallare la replica lasciando il database SQL di Azure come sistema di produzione.

 ![Diagramma di SeedCloudTR](./media/sql-database-cloud-migrate/SeedCloudTR.png)


La replica transazionale è una tecnologia integrata in SQL Server a partire dalla versione 6.5. Si tratta di una tecnologia sperimentata e consolidata che la maggior parte degli amministratori di database conosce e con cui hanno esperienza. Con l'[anteprima di SQL Server 2016](http://www.microsoft.com/server-cloud/products/sql-server-2016/), è ora possibile configurare il database SQL di Azure come [sottoscrittore di replica transazionale](https://msdn.microsoft.com/library/mt589530.aspx) alla pubblicazione locale. L'esperienza che si ottiene impostando il database da Management Studio è identica alla configurazione di un sottoscrittore di replica transazionale in un server locale. Questo scenario è supportato quando il server di pubblicazione e di distribuzione è almeno una delle seguenti versioni di SQL Server:

 - SQL Server 2016 CTP3 (anteprima) e versioni successive 
 - SQL Server 2014 SP1 CU3 e versioni successive
 - SQL Server 2014 RTM CU10 e versioni successive
 - SQL Server 2012 SP2 CU8 e versioni successive
 - SQL Server 2013 SP3 quando verrà rilasciata


> [AZURE.IMPORTANT] Usare sempre la versione più aggiornata di SQL Server Management Studio per restare sincronizzati con gli aggiornamenti di Microsoft Azure e del database SQL. Le versioni precedenti di SQL Server Management Studio non sono in grado di impostare il database SQL come sottoscrittore. [Aggiornare SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).


È inoltre possibile usare la replica transazionale per eseguire la migrazione di un subset del database locale. La pubblicazione di cui si esegue la replica nel database SQL di Azure può essere limitata a un subset delle tabelle nel database replicato. Inoltre, per ogni tabella replicata, è possibile limitare i dati a un subset di righe e/o di colonne.

<!---HONumber=AcomDC_0518_2016-->
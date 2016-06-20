<properties
   pageTitle="Migrare il database del server SQL Server al database SQL tramite la procedura guidata Distribuire il database nel database di Microsoft Azure | Microsoft Azure"
   description="Database SQL di Microsoft Azure, migrazione del database, creazione guidata database di Microsoft Azure"
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

# Eseguire la migrazione del database del server SQL al database SQL tramite la procedura guidata Distribuire il database nel database di Microsoft Azure


> [AZURE.SELECTOR]
- [Migrazione guidata in SSMS](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
- [Esportare in un file BACPAC.](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [Importare da file BACPAC.](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [Replica transazionale](sql-database-cloud-migrate-compatible-using-transactional-replication.md)

La procedura guidata Distribuire il database nel database di Microsoft Azure in SQL Server Management Studio esegue la migrazione di un [database compatibile di SQL Server](sql-database-cloud-migrate.md) direttamente nel server del database SQL.

## Usare la procedura guidata Distribuire il database nel database di Microsoft Azure

> [AZURE.NOTE] La procedura seguente presuppone la presenza di un [server del database SQL con provisioning](https://azure.microsoft.com/documentation/learning-paths/sql-database-training-learn-sql-database/).

1. Verificare di essere in possesso della versione più recente di SQL Server Management Studio. Le nuove versioni di Management Studio vengono aggiornate ogni mese per rimanere sincronizzate con gli aggiornamenti apportati al portale di Azure.

    > [AZURE.IMPORTANT] È consigliabile usare sempre la versione più aggiornata di Management Studio per restare sincronizzati con gli aggiornamenti di Microsoft Azure e del database SQL. [Aggiornare SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

2. Aprire Management Studio e connettersi al database del server SQL da migrare in Esplora oggetti.
3. Fare clic con il pulsante destro del mouse sul database in Esplora oggetti, scegliere **Attività**, quindi fare clic su **Distribuisci il database nel database SQL di Microsoft Azure**.

	![Distribuire in Azure dal menu Attività](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard01.png)

4.	Nella distribuzione guidata, fare clic su **Avanti** e quindi su **Connetti** per configurare la connessione al server del database SQL.

	![Distribuire in Azure dal menu Attività](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard002.png)

5. Nella finestra di dialogo Connessione al server, immettere le informazioni di connessione per connettersi al server del database SQL.

	![Distribuire in Azure dal menu Attività](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard00.png)

5.	Specificare **Nome nuovo database** per il nome del nuovo database, impostare **Edizione del database SQL di Microsoft Azure** ([livello del servizio](sql-database-service-tiers.md)), **Dimensioni massime database**, **Obiettivo servizio** (livello di prestazioni) e **Nome file temporaneo** per il file [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) creato dalla procedura guidata durante il processo di migrazione.

	![Impostazioni di esportazione](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard02.png)

6.	Completare la procedura guidata per la migrazione del database. A seconda delle dimensioni e della complessità del database la distribuzione può richiedere da pochi minuti a diverse ore. Se questa procedura guidata rileva i problemi di compatibilità, gli errori verranno visualizzati sullo schermo e la migrazione non continuerà. Per indicazioni su come risolvere i problemi di compatibilità del database, passare a [Risolvere i problemi di compatibilità del database](sql-database-cloud-migrate-fix-compatibility-issues.md).

7.	Usando Esplora oggetti, connettersi al database sottoposto a migrazione nel server del database SQL di Azure.
8.	Usando il portale di Azure visualizzare il database e le relative proprietà.

## Passaggi successivi

- [Scaricare la versione più recente di SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx)
- [Scaricare SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)

## Altre informazioni

- [Novità della versione 12 del database SQL](sql-database-v12-whats-new.md)
- [Transact-SQL partially or unsupported functions](sql-database-transact-sql-information.md) (Funzionalità di Transact-SQL parzialmente supportate o non supportate)
- [Migrate non-SQL Server databases using SQL Server Migration Assistant (Eseguire la migrazione di database non SQL Server mediante SQL Server Migration Assistant)](http://blogs.msdn.com/b/ssma/)

<!---HONumber=AcomDC_0608_2016-->
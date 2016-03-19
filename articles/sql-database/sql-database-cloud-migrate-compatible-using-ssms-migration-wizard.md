<properties
   pageTitle="Eseguire la migrazione del database del server SQL al database SQL tramite la procedura guidata Distribuire il database nel database di Microsoft Azure"
   description="Database SQL di Microsoft Azure, migrazione del database, creazione guidata database di Microsoft Azure"
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jeffreyg"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="12/17/2015"
   ms.author="carlrab"/>

# Eseguire la migrazione del database del server SQL al database SQL tramite la procedura guidata Distribuire il database nel database di Microsoft Azure

La procedura guidata Distribuire il database nel database di Microsoft Azure in SQL Server Management Studio esegue la migrazione di un [database compatibile di SQL Server](../sql-database-cloud-migrate.md) direttamente nel server del database SQL.

## Usare la procedura guidata Distribuire il database nel database di Microsoft Azure

> [AZURE.NOTE]La procedura seguente presuppone la presenza di un [server del database SQL con provisioning](../sql-database-get-started.md).

1. Verificare di essere in possesso della versione più recente di SQL Server Management Studio. Le nuove versioni di Management Studio vengono aggiornate ogni mese per rimanere sincronizzate con gli aggiornamenti apportati al portale di Azure.

    > [AZURE.IMPORTANT]È consigliabile usare sempre la versione più aggiornata di Management Studio per restare sincronizzati con gli aggiornamenti di Microsoft Azure e del database SQL. [Aggiornare SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

2. Aprire Management Studio e connettersi al database del server SQL da migrare in Esplora oggetti.
3. Fare clic con il pulsante destro del mouse sul database in Esplora oggetti, scegliere **Attività**, quindi fare clic su **Distribuisci il database nel database SQL di Microsoft Azure**.

	![Distribuire in Azure dal menu Attività](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard01.png)

4.	Nella distribuzione guidata, fare clic su **Avanti** e quindi su **Connetti** per configurare la connessione al server del database SQL.

	![Distribuire in Azure dal menu Attività](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard002.png)

5. Nella finestra di dialogo Connessione al server, immettere le informazioni di connessione per connettersi al server del database SQL.

	![Distribuire in Azure dal menu Attività](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard00.png)

5.	Specificare **Nome nuovo database** per il nome del nuovo database, impostare **Edizione del database SQL di Microsoft Azure** ([livello del servizio](sql-database-service-tiers.md)), **Dimensioni massime database**, **Obiettivo servizio** (livello di prestazioni) e **Nome file temporaneo** per il file [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) creato dalla procedura guidata durante il processo di migrazione.

	![Impostazioni di esportazione](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard02.png)

6.	Completare la procedura guidata per la migrazione del database. A seconda delle dimensioni e della complessità del database la distribuzione può richiedere da pochi minuti a diverse ore. Se questa procedura guidata rileva i problemi di compatibilità, gli errori verranno visualizzati sullo schermo e la migrazione non continuerà. Per indicazioni su come risolvere i problemi di compatibilità del database, passare a [Risolvere i problemi di compatibilità del database](sql-database-cloud-migrate-fix-compatibility-issues).

7.	Usando Esplora oggetti, connettersi al database sottoposto a migrazione nel server del database SQL di Azure.
8.	Usando il portale di Azure visualizzare il database e le relative proprietà.

## Passaggio successivo: risolvere i problemi di compatibilità, se presenti

[Risolvere i problemi di compatibilità del database](sql-database-cloud-migrate-fix-compatibility-issues.md), se presenti.

<!---HONumber=AcomDC_1223_2015-->
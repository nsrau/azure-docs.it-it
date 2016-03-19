<properties
   pageTitle="Migrazione di un database SQL Server nel database SQL di Azure"
   description="Database SQL di Microsoft Azure, distribuzione del database, migrazione del database, importazione del database, esportazione del database, migrazione guidata"
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

# Importare da BACPAC al database SQL tramite SSMS

> [AZURE.SELECTOR]
- [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
- [Azure Portal](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)

In questo articolo viene illustrato come importare da un file [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) a un database SQL mediante la procedura guidata di esportazione dell’applicazione livello dati in SQL Server Management Studio.

> [AZURE.NOTE]Il passaggio precedente presuppone che sia già stato effettuato il provisioning dell'istanza logica del database SQL di Azure e che le informazioni di connessione siano disponibili.

1. Verificare di essere in possesso della versione più recente di SQL Server Management Studio. Le nuove versioni di Management Studio vengono aggiornate ogni mese per rimanere sincronizzate con gli aggiornamenti apportati al portale di Azure.

	 >[AZURE.IMPORTANT]È consigliabile usare sempre la versione più aggiornata di Management Studio per restare sincronizzati con gli aggiornamenti di Microsoft Azure e del database SQL. [Aggiornare SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

2. Aprire Management Studio e connettersi al database di origine in Esplora oggetti.

	![Esportare un'applicazione livello dati dal menu Attività](./media/sql-database-cloud-migrate/MigrateUsingBACPAC01.png)

3. Dopo aver creato il file BACPAC, connettersi al server del database SQL di Azure, fare clic con il pulsante destro del mouse sulla cartella **Database** e quindi scegliere **Importa applicazione livello dati**.

    ![Importare la voce di menu dell’applicazione livello dati](./media/sql-database-cloud-migrate/MigrateUsingBACPAC03.png)

4.	Nell’importazione guidata selezionare il file BACPAC appena esportato per creare il nuovo database in database SQL di Azure.

    ![Importare impostazioni](./media/sql-database-cloud-migrate/MigrateUsingBACPAC04.png)

5.	Specificare il **Nome nuovo database** nel database SQL di Azure, impostare **Edizione del database SQL di Microsoft Azure** (livello del servizio), **Dimensioni massime database** e **Obiettivo servizio** (livello di prestazioni).

    ![Impostazioni database](./media/sql-database-cloud-migrate/MigrateUsingBACPAC05.png)

6.	Fare clic su **Avanti** e quindi su **Fine** per importare il file BACPAC in un nuovo database nel server del database SQL di Azure.

7. Usando Esplora oggetti, connettersi al database sottoposto a migrazione nel server del database SQL di Azure.

8.	Usando il portale di Azure visualizzare il database e le relative proprietà.

<!---HONumber=AcomDC_1223_2015-->
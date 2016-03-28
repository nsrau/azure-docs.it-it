<properties
	pageTitle="Restore a single table from Azure SQL Database backup | Microsoft Azure"
	description="Learn how to restore a single table from Azure SQL Database backup."
	services="sql-database"
	documentationCenter=""
	authors="dalechen"
	manager="felixwu"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/11/2016"
	ms.author="daleche"/>


# Come ripristinare una singola tabella nel backup del database SQL di Azure

Potrebbe verificarsi una situazione in cui alcuni dati sono stati modificati accidentalmente in un database SQL e ora si desidera ripristinare la singola tabella interessata. Questo articolo descrive la modalità di ripristino di una singola tabella in un database da uno dei backup eseguiti automaticamente dal database SQL di Azure, in base al livello di prestazioni selezionato.

## Passaggi preliminari: rinominare la tabella e ripristinare una copia del database
1. Identificare la tabella nel database SQL di Azure che si desidera sostituire con la copia ripristinata. Utilizzare Microsoft SQL Management Studio per rinominare la tabella. Ad esempio, rinominare la tabella come & lt; nome tabella &gt;\_vecchia.

	**Nota** Per evitare il blocco, assicurarsi che non vi sia alcuna attività in esecuzione nella tabella che si sta rinominando. In caso di problemi, accertarsi di eseguire questa procedura durante una finestra di manutenzione.

2. Ripristinare un backup del database temporizzato. A tale scopo, vedere i passaggi in [Ripristinare un database SQL di Azure a seguito di un errore causato dall'utente](../sql-database/sql-database-user-error-recovery.md).

	**Note**:
	- Il nome del database ripristinato sarà nel formato DBName+TimeStamp; ad esempio, **Adventureworks2012\_2016-01-01T22-12Z**. In questo passaggio non verrà sovrascritto il nome del database esistente nel server. Si tratta di una misura di sicurezza che consente all'utente di verificare il database ripristinato prima di eliminare il database corrente e rinominare il database ripristinato per l'uso in produzione.
	- Il servizio esegue il backup automatico di tutti i livelli di prestazioni, da Basic a Premium, con varie metriche di conservazione dei backup, a seconda del livello:

| Ripristino DB | Livello Basic | Livelli Standard | Livelli Premium |
| :-- | :-- | :-- | :-- |
| Ripristino temporizzato | Qualsiasi punto di ripristino entro 7 giorni|Qualsiasi punto di ripristino entro 14 giorni| Qualsiasi punto di ripristino entro 35 giorni|

## Copia della tabella dal database ripristinato tramite lo strumento di migrazione del database SQL
1. Scaricare e installare la [Migrazione guidata database SQL](https://sqlazuremw.codeplex.com).

2. Aprire la migrazione guidata database SQL, nella pagina **Select Process (Seleziona processo)**, selezionare **Database sotto Analyze/Migrate (Analizza/Migra)**, quindi fare clic su **Next (Avanti)**. ![SQL Database Migration wizard - Select Process](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/1.png)
3. Nella finestra di dialogo **Connect to Server (Connetti al server)** immettere i valori seguenti:
 - **Server name (Nome server)**: istanza di SQL Azure
 - **Authentication (Autenticazione)**: **autenticazione di SQL Server**. Immettere le credenziali di accesso.
 - **Database**: **Master DB (List all databases) (Database master (elenca tutti i database))**.
 - **Nota** per impostazione predefinita la procedura guidata salva le informazioni di accesso. Per modificare tale impostazione, selezionare **Forget Login Information (Annulla memorizzazione delle informazioni di accesso)**. ![SQL Database Migration wizard - Select Source - step 1](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/2.png)
4. Nella finestra di dialogo **Select Source (Seleziona origine)** selezionare il nome del database ripristinato nella sezione **Passaggi preliminari** come origine, quindi fare clic su **Next (Avanti)**.

	![SQL Database Migration wizard - Select Source - step 2](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/3.png)

5. Nella finestra di dialogo **Choose Objects (Seleziona oggetti)** selezionare l'opzione **Select specific database objects (Seleziona oggetti di database specifici)**, quindi selezionare una o più tabelle da migrare al server di destinazione. ![SQL Database Migration wizard - Choose Objects](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/4.png)

6. Nella pagina **Script Wizard Summary (Riepilogo Generazione guidata script)** fare clic su **Yes (Sì)** quando verrà richiesto se si è pronti a generare uno script SQL. È inoltre possibile salvare lo script TSQL per un utilizzo successivo. ![SQL Database Migration wizard - Script Wizard Summary](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/5.png)

7. Nella pagina **Results Summary (Riepilogo risultati)** fare clic su **Next (Avanti)**. ![SQL Database Migration wizard - Results Summary](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/6.png)

8. Nella pagina **Setup Target Server Connection (Configura connessione a server di destinazione)** fare clic su **Connect to Server (Connetti al server)**, quindi immettere i dettagli seguenti:
	- **Server Name (Nome server)**: istanza server di destinazione
	- **Authentication (Autenticazione)**: **autenticazione di SQL Server**. Immettere le credenziali di accesso.
	- **Database**: **Master DB (List all databases) (Database master (elenca tutti i database))**. Questa opzione consente di elencare tutti i database nel server di destinazione.

	![SQL Database Migration wizard - Setup Target Server Connection](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/7.png)

9. Fare clic su **Connect (Connetti)**, selezionare il database di destinazione in cui si desidera spostare la tabella e fare clic su **Next (Avanti)**. In tal modo si dovrebbe interrompere l'esecuzione dello script generato in precedenza e dovrebbe essere visualizzata la tabella appena spostata copiata nel database di destinazione.

## Passaggio di verifica
1. Eseguire query e verificare la tabella appena copiata per assicurarsi che i dati siano inalterati. Dopo la conferma, è possibile rimuovere la tabella rinominata nella sezione **Passaggi preliminari** (ad esempio, &lt;nome tabella&gt;\_vecchia).

<!---HONumber=AcomDC_0316_2016-->
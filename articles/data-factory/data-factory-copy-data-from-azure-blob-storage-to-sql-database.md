<properties
	pageTitle="Copiare dati da un archivio BLOB al database SQL | Microsoft Azure"
	description="Questa esercitazione illustra come usare l'attività di copia in una pipeline di Azure Data Factory per copiare i dati da un archivio BLOB a un database SQL."
	Keywords="blob sql, archivio blob, copia di dati"
	services="data-factory"
	documentationCenter=""
	authors="spelluru"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="data-factory"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article" 
	ms.date="08/01/2016"
	ms.author="spelluru"/>

# Copiare dati da un archivio BLOB al database SQL usando Data Factory 
> [AZURE.SELECTOR]
- [Panoramica dell'esercitazione](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Con l'editor di Data factory](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Tramite PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
- [Con Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
- [Con la Copia guidata](data-factory-copy-data-wizard-tutorial.md)

In questa esercitazione si creerà una data factory con una pipeline per copiare i dati dall'archivio BLOB al database SQL.

L'attività di copia esegue lo spostamento dei dati in Azure Data Factory e si basa su un servizio disponibile a livello globale che può copiare dati tra diversi archivi dati in modo sicuro, affidabile e scalabile. Per informazioni dettagliate sull'attività di copia, vedere [Attività di spostamento dei dati](data-factory-data-movement-activities.md).

> [AZURE.NOTE] Per una panoramica dettagliata del servizio Data factory, vedere l'articolo [Introduzione al servizio Data factory di Azure][data-factory-introduction] .

##Prerequisiti per l'esercitazione
Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

- **Sottoscrizione di Azure**. Se non è disponibile una sottoscrizione, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere l'articolo [Versione di valutazione gratuita][azure-free-trial].
- **Account di archiviazione di Azure**. In questa esercitazione l'archivio BLOB verrà usato come archivio dati di **origine**. Se non si ha un account di archiviazione di Azure, vedere l'articolo [Creare un account di archiviazione][data-factory-create-storage] per informazioni su come crearne uno.
- **Database SQL di Azure**. In questa esercitazione verrà usato un database SQL di Azure come archivio dati di **destinazione**. Se non è disponibile un database SQL di Azure da usare nell'esercitazione, vedere [Come creare e configurare un database SQL di Azure][data-factory-create-sql-database] per crearne uno.
- **SQL Server 2012/2014 o Visual Studio 2013**. Per creare un database di esempio e per visualizzare i dati risultanti in tale database, verrà usato SQL Server Management Studio o Visual Studio.

## Raccogliere il nome dell'account e la chiave dell'archivio BLOB 
Per eseguire questa esercitazione, saranno necessari il nome e la chiave dell'account di archiviazione di Azure. Annotare il **nome** e la **chiave** per l'account di archiviazione di Azure seguendo queste istruzioni:

1. Eseguire l'accesso al [portale di Azure][azure-portal].
2. Fare clic sull'hub **SFOGLIA** a sinistra e selezionare **Account di archiviazione**.
3. Nel pannello **Account di archiviazione** selezionare l'**account di archiviazione di Azure** da usare in questa esercitazione.
4. Selezionare **Chiavi di accesso** in **IMPOSTAZIONI**.
5.  Fare clic sul pulsante **copia** (immagine) accanto alla casella di testo **Nome dell'account di archiviazione** e salvarlo/incollarlo, ad esempio, in un file di testo.
6. Ripetere il passaggio precedente per copiare o annotare la chiave denominata **key1**.
7. Fare clic su **X** per chiudere tutti i pannelli.

## Raccogliere i nomi del server, del database e dell'utente per il database SQL
Per eseguire questa esercitazione, saranno necessari i nomi del server, del database e dell'utente di Azure SQL. Annotare i nomi di **server**, **database** e **utente** per il database SQL di Azure seguendo queste istruzioni:

1. Nel **portale di Azure** fare clic su **SFOGLIA** a sinistra e quindi selezionare **Database SQL**.
2. Nel pannello **Database SQL** selezionare il **database** da usare nell'esercitazione. Annotare il **nome database**.
3. Nel pannello **DATABASE SQL** fare clic sul riquadro **PROPRIETÀ**.
4. Annotare i valori per **NOME SERVER** e **NOME DI ACCESSO AMMINISTRATORE SERVER**.
5. Fare clic su **X** per chiudere tutti i pannelli.

## Consentire ai servizi di Azure di accedere a SQL Server 
Assicurarsi che l'impostazione **Consenti l'accesso a Servizi di Azure** sia **ATTIVA** per il server di Azure SQL, in modo che il servizio Data factory possa accedere al server di Azure SQL. Per verificare e attivare l'impostazione, eseguire le operazioni seguenti:

1. Fare clic sull'hub **SFOGLIA** a sinistra, quindi su **Server SQL**.
2. Selezionare il **server** e quindi fare clic su **IMPOSTAZIONI** nel pannello **SERVER SQL**.
3. Nel pannello **IMPOSTAZIONI** fare clic su **Firewall**.
4. Nel pannello **Impostazioni firewall** fare clic su **ATTIVA** per **Consenti accesso ai servizi Azure**.
5. Fare clic su **X** per chiudere tutti i pannelli.

## Preparare l'archivio BLOB e il database SQL 
Preparare ora l'archivio BLOB di Azure e il database SQL Azure per l'esercitazione seguendo questa procedura:

1. Avviare il Blocco note, incollare il testo seguente e salvarlo come file **emp.txt** nella cartella **C:\\ADFGetStarted** sul disco rigido.

        John, Doe
		Jane, Doe

2. Usare strumenti come [Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/) per creare il contenitore **adftutorial** e per caricare il file **emp.txt** nel contenitore.

    ![Azure Storage Explorer Copiare dati da un archivio BLOB al database SQL](./media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/getstarted-storage-explorer.png)  
3. Usare il seguente script SQL per creare la tabella **emp** nel database SQL di Azure.


        CREATE TABLE dbo.emp
		(
			ID int IDENTITY(1,1) NOT NULL,
			FirstName varchar(50),
			LastName varchar(50),
		)
		GO

		CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);

	**Se nel computer è installato SQL Server 2012/2014:** seguire le istruzioni riportate nel [Passaggio 2: Connettersi al database SQL dell'articolo Gestione di database SQL di Azure tramite SQL Server Management Studio][sql-management-studio] per connettersi al server SQL di Azure ed eseguire lo script SQL. Si noti che, per configurare il firewall per un server di Azure SQL, questo articolo usa il [portale di Azure classico](http://manage.windowsazure.com), non il [portale di Azure](https://portal.azure.com).

	**Se nel computer è installato Visual Studio 2013:** nel [portale di Azure](https://portal.azure.com) fare clic sull'hub **SFOGLIA** a sinistra, fare clic su **Server SQL**, selezionare il database e quindi fare clic sul pulsante **Apri in Visual Studio** sulla barra degli strumenti per connettersi al server di Azure SQL ed eseguire lo script. Se il client non è autorizzato ad accedere al server SQL di Azure, sarà necessario configurare il firewall per il server SQL di Azure in modo da consentire l'accesso dal computer (indirizzo IP). Per informazioni sulla procedura per configurare il firewall per il server SQL di Azure, vedere l'articolo precedente.


Eseguire le operazioni seguenti:

- Fare clic sul collegamento [Tramite l'editor di Data factory](data-factory-copy-activity-tutorial-using-azure-portal.md) in alto per eseguire l'esercitazione usando l'Editor di Data factory, incluso nel portale di Azure.
- Fare clic sul collegamento [Tramite PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) in alto per eseguire l'esercitazione usando Azure PowerShell.
- Fare clic sul collegamento [Tramite Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) in alto per eseguire l'esercitazione usando Visual Studio 2013.

## l'attività di copia
Vedere [Attività di spostamento dei dati](data-factory-data-movement-activities.md) per informazioni dettagliate sull'Attività di copia in Azure Data Factory.


<!--Link references-->
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-portal]: https://portal.azure.com/
[sql-management-studio]: http://azure.microsoft.com/documentation/articles/sql-database-manage-azure-ssms/#Step2

[data-factory-introduction]: data-factory-introduction.md
[data-factory-create-storage]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/#create-a-storage-account
[data-factory-create-sql-database]: ../sql-database/sql-database-get-started.md

<!---HONumber=AcomDC_0810_2016-->
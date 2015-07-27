<properties
	pageTitle="Introduzione all'uso di Data factory di Azure"
	description="Questa esercitazione illustra come creare una pipeline di dati di esempio che consente di copiare dati da un BLOB a un'istanza del database SQL di Azure."
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
	ms.topic="hero-article" 
	ms.date="05/04/2015"
	ms.author="spelluru"/>

# Introduzione a Data factory di Azure
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-get-started.md)
- [Using Data Factory Editor](data-factory-get-started-using-editor.md)
- [Using PowerShell](data-factory-monitor-manage-using-powershell.md)

L'esercitazione in questo articolo permette di iniziare subito a usare il servizio Data factory di Azure. In questa esercitazione verranno create una data factory di Azure e una pipeline nella data factory per copiare i dati da un archivio BLOB di Azure a un database SQL di Azure.

> [AZURE.NOTE]Per una panoramica dettagliata del servizio Data factory, vedere l'articolo [Introduzione al servizio Data factory di Azure][data-factory-introduction] .

##Prerequisiti per l'esercitazione
Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

- **Sottoscrizione di Azure**. Se non è disponibile una sottoscrizione, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere l'articolo [Versione di valutazione gratuita][azure-free-trial].
- **Account di archiviazione di Azure**. In questa esercitazione l'archivio BLOB verrà usato come archivio dati di **origine**. Se non si ha un account di archiviazione di Azure, vedere l'articolo [Creare un account di archiviazione][data-factory-create-storage] per informazioni su come crearne uno.
- **Database SQL di Azure**. In questa esercitazione verrà usato un database SQL di Azure come archivio dati di **destinazione**. Se non è disponibile un database SQL di Azure da usare nell'esercitazione, vedere [Come creare e configurare un database SQL di Azure][data-factory-create-sql-database] per crearne uno.
- **SQL Server 2012/2014 o Visual Studio 2013**. Per creare un database di esempio e per visualizzare i dati risultanti in tale database, verrà usato SQL Server Management Studio o Visual Studio.  

### Raccogliere il nome e la chiave dell'account di archiviazione di Azure
Per eseguire questa esercitazione, saranno necessari il nome e la chiave dell'account di archiviazione di Azure. Annotare il **nome** e la **chiave** per l'account di archiviazione di Azure seguendo queste istruzioni:

1. Accedere al [portale di anteprima di Azure][azure-preview-portal].
2. Fare clic sull'hub **SFOGLIA** a sinistra e selezionare **Account di archiviazione**.
3. Nel pannello **Account di archiviazione** selezionare l'**account di archiviazione di Azure** da usare in questa esercitazione.
4. Nel pannello **ARCHIVIAZIONE** fare clic sul riquadro **CHIAVI**.
5. Nel pannello **Gestisci chiavi** fare clic sul pulsante **copia** (immagine) accanto alla casella di testo **NOME ACCOUNT DI ARCHIVIAZIONE** e salvarlo/incollarlo, ad esempio, in un file di testo.  
6. Ripetere il passaggio precedente per copiare o annotare la **CHIAVE DI ACCESSO PRIMARIA**.
7. Fare clic su **X** per chiudere tutti i pannelli.

### Raccogliere il nome server, il nome database e l'account utente per il database SQL di Azure
Per eseguire questa esercitazione, saranno necessari i nomi del server, del database e dell'utente di Azure SQL. Annotare i nomi di **server**, **database** e **utente** per il database SQL di Azure seguendo queste istruzioni:

1. Nel **portale di anteprima di Azure** fare clic su **SFOGLIA** a sinistra e quindi selezionare **Database SQL**.
2. Nel pannello **Database SQL** selezionare il **database** da usare nell'esercitazione. Annotare il **nome database**.  
3. Nel pannello **DATABASE SQL** fare clic sul riquadro **PROPRIETÀ**.
4. Annotare i valori per **NOME SERVER** e **NOME DI ACCESSO AMMINISTRATORE SERVER**.
5. Fare clic su **X** per chiudere tutti i pannelli.

### Consentire ai servizi di Azure di accedere al server di Azure SQL
Assicurarsi che l'impostazione **Consenti l'accesso a Servizi di Azure** sia **ATTIVA** per il server di Azure SQL, in modo che il servizio Data factory possa accedere al server di Azure SQL. Per verificare e attivare l'impostazione, eseguire le operazioni seguenti:

1. Fare clic sull'hub **SFOGLIA** a sinistra, quindi su **Server SQL**.
2. Selezionare il **server** e quindi fare clic su **IMPOSTAZIONI** nel pannello **SERVER SQL**.
3. Nel pannello **IMPOSTAZIONI** fare clic su **Firewall**.
4. Nel pannello **Impostazioni firewall** fare clic su **ATTIVA** per **Consenti accesso ai servizi Azure**.
5. Fare clic su **X** per chiudere tutti i pannelli.

### Preparare l'archiviazione BLOB di Azure e il database SQL Azure per l'esercitazione
Preparare ora l'archivio BLOB di Azure e il database SQL Azure per l'esercitazione seguendo questa procedura:

1. Avviare il Blocco note, incollare il testo seguente e salvarlo come file **emp.txt** nella cartella **C:\\ADFGetStarted** sul disco rigido.

        John, Doe
		Jane, Doe

2. Usare strumenti come [Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/) per creare il contenitore **adftutorial** e per caricare il file **emp.txt** nel contenitore.

    ![Esplora archivi Azure][image-data-factory-get-started-storage-explorer]
3. Usare il seguente script SQL per creare la tabella **emp** nel database SQL di Azure.  


        CREATE TABLE dbo.emp
		(
			ID int IDENTITY(1,1) NOT NULL,
			FirstName varchar(50),
			LastName varchar(50),
		)
		GO

		CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);

	**Se nel computer è installato SQL Server 2012/2014:** seguire le istruzioni riportate nel [Passaggio 2: Connettersi al database SQL dell'articolo Gestione di database SQL di Azure tramite SQL Server Management Studio][sql-management-studio] per connettersi al server SQL di Azure ed eseguire lo script SQL. Si noti che, per configurare il firewall per un server di Azure SQL, in questo articolo viene usato il portale di gestione della versione rilasciata (http://manage.windowsazure.com), non il portale di anteprima (http://portal.azure.com).

	**Se nel computer è installato Visual Studio 2013:** nel [portale di anteprima di Azure](http://portal.azure.com) fare clic sull'hub **SFOGLIA** a sinistra, fare clic su **Server SQL**, selezionare il database e quindi fare clic sul pulsante **Apri in Visual Studio** sulla barra degli strumenti per connettersi al server SQL di Azure ed eseguire lo script. Se il client non è autorizzato ad accedere al server SQL di Azure, sarà necessario configurare il firewall per il server SQL di Azure in modo da consentire l'accesso dal computer (indirizzo IP). Per informazioni sulla procedura per configurare il firewall per il server SQL di Azure, vedere l'articolo precedente.


Eseguire le operazioni seguenti:

- Fare clic sul collegamento [Tramite l'editor di Data factory](data-factory-get-started-using-editor.md) in alto per eseguire l'esercitazione usando l'Editor di Data factory, incluso nel portale di Azure.
- Fare clic sul collegamento [Tramite PowerShell](data-factory-monitor-manage-using-powershell.md) in alto per eseguire l'esercitazione usando Azure PowerShell.


<!--Link references-->
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[msdn-activities]: https://msdn.microsoft.com/library/dn834988.aspx
[msdn-linkedservices]: https://msdn.microsoft.com/library/dn834986.aspx
[data-factory-naming-rules]: https://msdn.microsoft.com/library/azure/dn835027.aspx

[azure-preview-portal]: https://portal.azure.com/
[download-azure-powershell]: http://azure.microsoft.com/documentation/articles/install-configure-powershell
[sql-management-studio]: http://azure.microsoft.com/documentation/articles/sql-database-manage-azure-ssms/#Step2
[sql-cmd-exe]: https://msdn.microsoft.com/library/azure/ee336280.aspx

[data-factory-editor]: data-factory-editor.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
[copy-activity]: data-factory-copy-activity.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[data-factory-create-storage]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/#create-a-storage-account
[data-factory-create-sql-database]: ../sql-database-create-configure.md


[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

<!--Image references-->

[DataSlicesBySliceTime]: ./media/data-factory-get-started/DataSlicesBySliceTime.png

[image-data-factory-getstarted-new-everything]: ./media/data-factory-get-started/GetStarted-New-Everything.png

[image-data-factory-gallery-storagecachebackup]: ./media/data-factory-get-started/getstarted-gallery-datastoragecachebackup.png

[image-data-factory-gallery-storagecachebackup-seeall]: ./media/data-factory-get-started/getstarted-gallery-datastoragecachebackup-seeall.png

[image-data-factory-getstarted-data-services-data-factory-selected]: ./media/data-factory-get-started/getstarted-data-services-data-factory-selected.png

[image-data-factory-getstarted-data-factory-create-button]: ./media/data-factory-get-started/getstarted-data-factory-create-button.png

[image-data-factory-getstarted-new-data-factory-blade]: ./media/data-factory-get-started/getstarted-new-data-factory.png

[image-data-factory-get-stated-factory-home-page]: ./media/data-factory-get-started/getstarted-data-factory-home-page.png

[image-author-deploy-tile]: ./media/data-factory-get-started/getstarted-author-deploy-tile.png

[image-editor-newdatastore-button]: ./media/data-factory-get-started/getstarted-editor-newdatastore-button.png

[image-editor-blob-storage-json]: ./media/data-factory-get-started/getstarted-editor-blob-storage-json.png

[image-editor-blob-storage-deploy]: ./media/data-factory-get-started/getstarted-editor-blob-storage-deploy.png

[image-editor-azure-sql-settings]: ./media/data-factory-get-started/getstarted-editor-azure-sql-settings.png

[image-editor-newpipeline-button]: ./media/data-factory-get-started/getstarted-editor-newpipeline-button.png

[image-datafactoryblade-diagramtile]: ./media/data-factory-get-started/getstarted-datafactoryblade-diagramtile.png


[image-data-factory-get-started-startboard]: ./media/data-factory-get-started/getstarted-data-factory-startboard.png

[image-data-factory-get-started-linked-services-link]: ./media/data-factory-get-started/getstarted-data-factory-linked-services-link.png

[image-data-factory-get-started-linked-services-add-store-button]: ./media/data-factory-get-started/getstarted-linked-services-add-store-button.png

[image-data-factory-linked-services-get-started-new-data-store]: ./media/data-factory-get-started/getstarted-linked-services-new-data-store.png

[image-data-factory-get-started-new-data-store-with-storage]: ./media/data-factory-get-started/getstarted-linked-services-new-data-store-with-storage.png

[image-data-factory-get-started-storage-account-name-key]: ./media/data-factory-get-started/getstarted-storage-account-name-key.png

[image-data-factory-get-started-linked-services-list-with-myblobstore]: ./media/data-factory-get-started/getstarted-linked-services-list-with-myblobstore.png

[image-data-factory-get-started-linked-azure-sql-properties]: ./media/data-factory-get-started/getstarted-linked-azure-sql-properties.png

[image-data-factory-get-started-azure-sql-connection-string]: ./media/data-factory-get-started/getstarted-azure-sql-connection-string.png

[image-data-factory-get-started-linked-services-list-two-stores]: ./media/data-factory-get-started/getstarted-linked-services-list-two-stores.png

[image-data-factory-get-started-storage-explorer]: ./media/data-factory-get-started/getstarted-storage-explorer.png

[image-data-factory-get-started-diagram-link]: ./media/data-factory-get-started/getstarted-diagram-link.png

[image-data-factory-get-started-diagram-blade]: ./media/data-factory-get-started/getstarted-diagram-blade.png

[image-data-factory-get-started-home-page-pipeline-tables]: ./media/data-factory-get-started/getstarted-datafactory-home-page-pipeline-tables.png

[image-data-factory-get-started-datasets-blade]: ./media/data-factory-get-started/getstarted-datasets-blade.png

[image-data-factory-get-started-table-blade]: ./media/data-factory-get-started/getstarted-table-blade.png

[image-data-factory-get-started-dataslices-blade]: ./media/data-factory-get-started/getstarted-dataslices-blade.png

[image-data-factory-get-started-dataslice-blade]: ./media/data-factory-get-started/getstarted-dataslice-blade.png

[image-data-factory-get-started-sql-query-results]: ./media/data-factory-get-started/getstarted-sql-query-results.png

[image-data-factory-get-started-datasets-emptable-selected]: ./media/data-factory-get-started/DataSetsWithEmpTableFromBlobSelected.png

[image-data-factory-get-started-activity-run-details]: ./media/data-factory-get-started/ActivityRunDetails.png

[image-data-factory-create-resource-group]: ./media/data-factory-get-started/CreateNewResourceGroup.png

[image-data-factory-preview-storage-key]: ./media/data-factory-get-started/PreviewPortalStorageKey.png

[image-data-factory-database-connection-string]: ./media/data-factory-get-started/DatabaseConnectionString.png

[image-data-factory-new-datafactory-menu]: ./media/data-factory-get-started/NewDataFactoryMenu.png

[image-data-factory-sql-management-console]: ./media/data-factory-get-started/getstarted-azure-sql-management-console.png

[image-data-factory-sql-management-console-2]: ./media/data-factory-get-started/getstarted-azure-sql-management-console-2.png

[image-data-factory-name-not-available]: ./media/data-factory-get-started/getstarted-data-factory-not-available.png
 

<!---HONumber=July15_HO3-->
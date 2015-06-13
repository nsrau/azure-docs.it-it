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

L'esercitazione in questo articolo consente di iniziare rapidamente con l'utilizzo del servizio Factory di dati di Azure. In questa esercitazione verrà creata una factory di dati di Azure e creare una pipeline nell'ambiente di produzione di dati per copiare dati da un'archiviazione blob di Azure a un database SQL Azure.

> [AZURE.NOTE]Per una panoramica dettagliata del servizio dati Factory, vedere il [Introduzione a Azure dati Factory][data-factory-introduction] articolo.

##Prerequisiti per l'esercitazione
Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

- **Sottoscrizione di Azure**. Se non si dispone di una sottoscrizione, è possibile creare un account di valutazione gratuita in pochi minuti. Vedere il [versione di valutazione gratuita][azure-free-trial] articolo per informazioni dettagliate.
- **Account di archiviazione azure**. Si utilizzerà l'archiviazione blob come un **origine** archivio dati in questa esercitazione. Se non è un account di archiviazione di Azure, vedere il [creare un account di archiviazione][data-factory-create-storage] articolo per la procedura per crearne uno.
- **Database SQL di Azure**. Si utilizzerà un database SQL Azure come un **destinazione** archivio dati in questa esercitazione. Se non si dispone di un database di SQL Azure è possibile utilizzare nell'esercitazione, vedere [come creare e configurare un Database di SQL Azure][data-factory-create-sql-database] per crearne uno.
- **SQL Server 2012/2014 o Visual Studio 2013**. Si utilizzerà SQL Server Management Studio o Visual Studio per creare un database di esempio e visualizzare i dati del risultato nel database.  

### Raccogliere il nome di account e la chiave dell'account per l'account di archiviazione di Azure
Sarà necessario il nome account e la chiave dell'account dell'account di archiviazione di Azure per eseguire questa esercitazione. Annotare il **nome account** e **chiave account** per l'account di archiviazione di Azure seguendo le istruzioni seguenti:

1. Account di accesso per il [portale Azure Preview][azure-preview-portal].
2. Fare clic su **Sfoglia** hub a sinistra e selezionare **gli account di archiviazione**.
3. Nel **gli account di archiviazione** blade, selezionare il **account di archiviazione Azure** che si desidera utilizzare in questa esercitazione.
4. Nel **archiviazione** blade, fare clic su **CHIAVI** riquadro.
5. Nel **gestione delle chiavi** blade, fare clic su **Copia** pulsante (immagine) accanto alla **nome ACCOUNT di archiviazione** testo casella e il salvataggio e incollarlo in un punto (ad esempio: in un file di testo).  
6. Ripetere il passaggio precedente per copiare o annotare il **chiave di accesso primaria**.
7. Chiudere tutti i server blade facendo clic su **X**.

### Raccogliere il nome del server, nome del database e account utente per il database SQL Azure
È necessario che i nomi dei server SQL Azure, database e utente per eseguire questa esercitazione. Annotare i nomi di **server**, **database**, e **utente** per il database di SQL Azure seguendo le istruzioni seguenti:

1. Nel **portale Azure Preview**, fare clic su **Sfoglia** a sinistra e selezionare **database SQL**.
2. Nel **blade di database SQL**, selezionare il **database** che si desidera utilizzare in questa esercitazione. Annotare il **nome del database**.  
3. Nel **DATABASE SQL** blade, fare clic su **proprietà** riquadro.
4. Annotare i valori per **nome SERVER** e **account di accesso amministratore SERVER**.
5. Chiudere tutti i server blade facendo clic su **X**.

### Consenti servizi di Azure accedere al server SQL Azure
Assicurarsi che **consentire l'accesso ai servizi Azure** impostazione spento **via** per il server SQL Azure in modo che il servizio dati Factory possa accedere al server SQL Azure. Per verificare e attivare questa impostazione, procedere come segue:

1. Fare clic su **Sfoglia** hub a sinistra e fare clic su **istanze di SQL Server**.
2. Selezionare **server**, e fare clic su **impostazioni** sul **SQL SERVER** blade.
3. Nel **impostazioni** blade, fare clic su **Firewall**.
4. Nel **le impostazioni del Firewall** blade, fare clic su **via** per **consentire l'accesso ai servizi Azure**.
5. Chiudere tutti i server blade facendo clic su **X**.

### Preparazione dell'archiviazione BLOB di Azure e del database SQL Azure per l'esercitazione
A questo punto, preparare l'archiviazione blob di Azure e il database SQL Azure per l'esercitazione eseguendo i passaggi seguenti:

1. Avviare Blocco note, incollare il testo seguente e salvarlo come **emp.txt** a **C:\ADFGetStarted** cartella sul disco rigido.

        John, Doe
		Jane, Doe

2. Utilizzare strumenti come [Esplora archivi Azure](https://azurestorageexplorer.codeplex.com/) per creare il **adftutorial** contenitore e per caricare il **emp.txt** file nel contenitore.

    ![Esplora archivi Azure][image-data-factory-get-started-storage-explorer]
3. Utilizzare il seguente script SQL per creare il **emp** tabella nel Database di SQL Azure.  


        CREATE TABLE dbo.emp
		(
			ID int IDENTITY(1,1) NOT NULL,
			FirstName varchar(50),
			LastName varchar(50),
		)
		GO

		CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);

	**Se si dispone di SQL Server 2012/2014 installato nel computer:** seguire le istruzioni da [passaggio 2: connettersi al Database SQL di gestione di Database SQL di Azure con SQL Server Management Studio][sql-management-studio] articolo per connettersi al server SQL Azure ed eseguire lo script SQL. Si noti che in questo articolo viene utilizzato il portale di gestione del rilascio (http://manage.windowsazure.com), il portale di anteprima (http://portal.azure.com), per configurare il firewall per un server SQL Azure.

	**Se si dispone di Visual Studio 2013 installato nel computer:** nel [portale Azure Preview](http://portal.azure.com), fare clic su **Sfoglia** hub sulla sinistra fare clic su **istanze di SQL Server**, selezionare il database e fare clic su **aperto in Visual Studio** sulla barra degli strumenti per connettersi al server SQL Azure ed eseguire lo script. Se il client non è consentito accedere al server SQL Azure, sarà necessario configurare il firewall per il server SQL Azure consentire l'accesso dal computer (indirizzo IP). Vedere l'articolo sopra per le procedure per configurare il firewall per il server SQL Azure.


Eseguire le operazioni seguenti:

- Fare clic su [utilizzando dati Factory Editor](data-factory-get-started-using-editor.md) collegamento nella parte superiore per eseguire l'esercitazione utilizzando dati Factory Editor, che fa parte del portale di Azure.
- Fare clic su [tramite PowerShell](data-factory-monitor-manage-using-powershell.md) collegamento nella parte superiore per eseguire l'esercitazione utilizzando Azure PowerShell.


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

<!---HONumber=GIT-SubDir--> 
<properties 
	pageTitle="Ripristinare un'app in Azure" 
	description="Informazioni su come ripristinare l'app da un backup." 
	services="app-service" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/06/2016" 
	ms.author="cephalin"/>

# Ripristinare un'app in Azure

Questo articolo illustra come ripristinare un'app nel [Servizio app di Azure](../app-service/app-service-value-prop-what-is.md) in precedenza sottoposta a un backup (vedere [Eseguire il backup di un'app Web nel servizio app di Azure](web-sites-backup.md)). È possibile ripristinare su richiesta uno stato precedente dell'app con i relativi database collegati (database SQL o MySQL) oppure creare una nuova app basata su uno dei backup dell'applicazione originale. Creare una nuova app eseguita in parallelo con l'ultima versione può essere utile per l'esecuzione di test A/B.

Il ripristino da backup è disponibile per le app in esecuzione a livello **Standard** e **Premium**. Per informazioni sul passaggio dell'app a un piano superiore, vedere [Scalare un'app Web in Servizio app di Azure](web-sites-scale.md). Il livello **Premium** consente un maggior numero di backup giornalieri rispetto al livello **Standard**.

<a name="PreviousBackup"></a>
## Ripristinare un'app da un backup esistente

1. Nel pannello **Impostazioni** dell'app nel portale di Azure, fare clic su **Backup** per visualizzare il pannello **Backup**. Quindi fare clic su **Ripristina ora** nella barra dei comandi.
	
	![Scegliere Ripristina][ChooseRestoreNow]

3. Nel pannello **Ripristina**, selezionare l'origine di backup.

	![](./media/web-sites-restore/021ChooseSource.png)
	
	L'opzione **Backup dell'app** mostra tutti i backup esistenti dell'app corrente, che possono essere facilmente selezionati. L'opzione **Archiviazione** consente di selezionare qualsiasi file ZIP del backup da un account di archiviazione e un contenitore di Azure esistenti nella sottoscrizione. Se si sta tentando di ripristinare un backup di un'altra app, usare l'opzione **Archiviazione**.

4. Quindi, specificare la destinazione per il ripristino dell’app in **Destinazione di ripristino**.

	![](./media/web-sites-restore/022ChooseDestination.png)
	
	>[AZURE.WARNING] Se si sceglie **Sovrascrivi**, tutti i dati esistenti nell'app corrente verranno cancellati. Prima di scegliere **OK**, assicurarsi che sia esattamente ciò che si desidera eseguire.
	
	È possibile selezionare **App esistente** per ripristinare il backup dell’app in un'altra applicazione nello stesso gruppo di risorse. Prima di utilizzare questa opzione, deve già essere stata creata un'altra app nel gruppo di risorse con mirroring della configurazione del database in quello definito nel backup dell’app.
	
5. Fare clic su **OK**.

<a name="StorageAccount"></a>
## Scaricare o eliminare un backup da un account di archiviazione
	
1. Dal pannello principale **Sfoglia** del portale di Azure, selezionare **Account di archiviazione**.
	
	Verrà visualizzato un elenco degli account di archiviazione esistenti.
	
2. Selezionare l'account di archiviazione che contiene il backup che si desidera scaricare o eliminare.
	
	Verrà visualizzato il pannello per l'account di archiviazione.

3. Nel pannello dell'account di archiviazione selezionare il contenitore desiderato
	
	![Contenitori di visualizzazione][ViewContainers]

4. Selezionare il file di backup che si desidera scaricare o eliminare.

	![ViewContainers](./media/web-sites-restore/03ViewFiles.png)

5. Fare clic su **Scarica** o **Elimina** a seconda dell'azione che si desidera eseguire.

<a name="OperationLogs"></a>
## Monitorare un'operazione di ripristino
	
1. Per visualizzare i dettagli sul successo o sulla mancata riuscita dell'operazione di ripristino dell'app, passare al pannello ** Log di controllo** nel portale di Azure.
	
	Nel pannello **Log di controllo** vengono visualizzate tutte le operazioni, insieme ai dettagli relativi a livello, stato, risorsa e tempo.
	
2. Scorrere verso il basso per trovare l'operazione di ripristino desiderata e fare clic su di essa per selezionarla.

Nel pannello dei dettagli verranno visualizzate le informazioni disponibili correlate all'operazione di ripristino.
	
## Passaggi successivi

È anche possibile eseguire il backup e il ripristino delle applicazioni del servizio app mediante l'API REST (vedere [Usare REST per eseguire il backup e il ripristino di app del servizio App](websites-csm-backup.md)).

>[AZURE.NOTE] Per iniziare a usare Servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.


<!-- IMAGES -->
[ChooseRestoreNow]: ./media/web-sites-restore/02ChooseRestoreNow.png
[ViewContainers]: ./media/web-sites-restore/03ViewContainers.png
[StorageAccountFile]: ./media/web-sites-restore/02StorageAccountFile.png
[BrowseCloudStorage]: ./media/web-sites-restore/03BrowseCloudStorage.png
[StorageAccountFileSelected]: ./media/web-sites-restore/04StorageAccountFileSelected.png
[ChooseRestoreSettings]: ./media/web-sites-restore/05ChooseRestoreSettings.png
[ChooseDBServer]: ./media/web-sites-restore/06ChooseDBServer.png
[RestoreToNewSQLDB]: ./media/web-sites-restore/07RestoreToNewSQLDB.png
[NewSQLDBConfig]: ./media/web-sites-restore/08NewSQLDBConfig.png
[RestoredContosoWebSite]: ./media/web-sites-restore/09RestoredContosoWebSite.png
[DashboardOperationLogsLink]: ./media/web-sites-restore/10DashboardOperationLogsLink.png
[ManagementServicesOperationLogsList]: ./media/web-sites-restore/11ManagementServicesOperationLogsList.png
[DetailsButton]: ./media/web-sites-restore/12DetailsButton.png
[OperationDetails]: ./media/web-sites-restore/13OperationDetails.png
 

<!---HONumber=AcomDC_0713_2016-->
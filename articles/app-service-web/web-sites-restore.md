<properties 
	pageTitle="Ripristinare un'app Web in Azure App Service" 
	description="description=";Informazioni su come ripristinare l'app Web da un backup.";" 
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
	ms.date="09/16/2015" 
	ms.author="cephalin"/>

# Ripristinare un'app Web in Azure App Service

In questo articolo viene illustrato come ripristinare un'app Web in precedenza sottoposta a un backup tramite la funzionalità di backup delle [app Web di App Service](http://go.microsoft.com/fwlink/?LinkId=529714). Per ulteriori informazioni, vedere [Backup delle app Web di App Service](web-sites-backup.md).

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

La funzionalità di ripristino delle app Web consente di ripristinare su richiesta lo stato precedente dell'app Web o di creare una nuova app Web in base ai backup dell'app Web originale. Creare un nuovo sito Web eseguito in parallelo con l'ultima versione può essere utile per l'esecuzione di test A/B.

La funzionalità di ripristino di App Web, presente nel pannello **Backup** del [portale di anteprima di Azure](http://portal.azure.com), è disponibile solo nelle modalità Standard e Premium. Per informazioni su come ridimensionare l'app in modalità Standard o Premium, vedere [Ridimensionare un'app Web nel servizio app di Azure](web-sites-scale.md). Si noti che la modalità Premium consente di eseguire un maggior numero di backup giornalieri rispetto alla modalità Standard.

<a name="PreviousBackup"></a>
## Per ripristinare un'app Web da un backup eseguito in precedenza

1. Nel pannello **Impostazioni** dell'app Web nel portale di Azure, fare clic sull'opzione **Backup** per visualizzare il pannello **Backup**. Scorrere nel pannello e selezionare una delle voci di backup in base all'**ORA DEL BACKUP** e allo **STATO** dall'elenco dei backup.
	
	![Choose backup source][ChooseBackupSource]
	
2. Selezionare **Ripristina ora** nella parte superiore del pannello **Backup**.

	![Scegliere Ripristina][ChooseRestoreNow]

3. Nel pannello **Ripristina**, per ripristinare l'app Web esistente, verificare tutti i dettagli visualizzati e fare clic su **OK**.
	
È inoltre possibile ripristinare l'app Web in una nuova app selezionando **APP WEB** dal pannello **Ripristina** e selezionando **Creare una nuova app Web**.
	
<a name="StorageAccount"></a>
## Scaricare o eliminare un backup da un account di archiviazione
	
1. Dal pannello principale **Sfoglia** del portale di Azure, selezionare **Account di archiviazione**.
	
	Verrà visualizzato un elenco degli account di archiviazione esistenti.
	
2. Selezionare l'account di archiviazione che contiene il backup che si desidera scaricare o eliminare.
	
	Verrà visualizzato il pannello **ARCHIVIAZIONE**.

3. Selezionare **Contenitori** nel pannello **ARCHIVIAZIONE** per visualizzare il pannello **Contenitori**.
	
	Verrà visualizzato un elenco dei contenitori. In questo elenco verranno inoltre mostrati l'URL e la data in cui il contenitore è stato modificato l'ultima volta.
	
	![Contenitori di visualizzazione][ViewContainers]

4. Nell'elenco, selezionare il contenitore e visualizzare il pannello in cui viene mostrato un elenco dei nomi di file, insieme alle dimensioni di ciascun file.
	
5. Selezionando un file, è possibile scegliere di **scaricarlo** oppure **eliminarlo**. Si noti che esistono due tipi di file principali, i file ZIP e gli XML.

<a name="OperationLogs"></a>
## Visualizzare i log di controllo
	
1. Per visualizzare i dettagli sul successo o sulla mancata riuscita dell'operazione di ripristino dell'app Web, selezionare **Log di controllo** nel pannello **Sfoglia** principale. 
	
	Nel pannello **Log audio** vengono visualizzate tutte le operazioni, insieme ai dettagli relativi a livello, stato, risorsa e tempo.
	
2. Scorrere il pannello per individuare le operazioni correlate all'app Web.
3. Per visualizzare altri dettagli su un'operazione, selezionare tale operazione nell'elenco.
	
Nel pannello dei dettagli verranno visualizzate le informazioni disponibli correlate all'operazione.
	
>[AZURE.NOTE]Per iniziare a usare Servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.
	
## Modifiche apportate
* Per una guida relativa al passaggio da Siti Web al servizio app, vedere [Servizio app di Azure e impatto sui servizi di Azure esistenti](http://go.microsoft.com/fwlink/?LinkId=529714)
* Per una Guida per la modifica del portale precedente per il nuovo portale, vedere: [riferimento per lo spostamento tra il portale di anteprima](http://go.microsoft.com/fwlink/?LinkId=529715)

<!-- IMAGES -->
[ChooseBackupSource]: ./media/web-sites-restore/01ChooseBackupSource.png
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
 

<!---HONumber=Oct15_HO3-->
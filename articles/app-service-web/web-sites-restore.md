<properties 
	pageTitle="Ripristinare un'app nel Servizio app di Azure" 
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
	ms.date="01/07/2015" 
	ms.author="cephalin"/>

# Ripristinare un'app nel Servizio app di Azure

In questo articolo viene illustrato come ripristinare un'app del servizio app in precedenza sottoposta a un backup tramite la funzionalità di backup del [servizio app](app-service-value-prop-what-is). Per altre informazioni, vedere [Backup del servizio app](web-sites-backup.md).

La funzionalità di ripristino del servizio app consente di ripristinare su richiesta uno stato precedente dell'app con i relativi database collegati (Database SQL o MySQL) oppure di creare una nuova app basata su uno dei backup dell'applicazione originale. Creare una nuova app eseguita in parallelo con l'ultima versione può essere utile per l'esecuzione di test A/B.

La funzionalità di ripristino del servizio app, disponibile nel pannello **Backup** del [portale di Azure](http://portal.azure.com), è disponibile solo nei piani tariffari Standard e Premium. Per informazioni su come ridimensionare l'app utilizzando i piani Standard o Premium, vedere [Ridimensionare un'app Web nel servizio app di Azure](web-sites-scale.md). Si noti che il piano Premium consente di eseguire un maggior numero di backup giornalieri rispetto al piano Standard.

<a name="PreviousBackup"></a>
## Per ripristinare un'app da un backup eseguito in precedenza

1. Nel pannello **Impostazioni** dell'app nel portale di Azure, fare clic su **Backup** per visualizzare il pannello **Backup**. Quindi fare clic su **Ripristina ora** nella barra dei comandi. 
	
	![Scegliere Ripristina][ChooseRestoreNow]

3. Nel pannello **Ripristina**, selezionare l'origine di backup.

	![](./media/web-sites-restore/021ChooseSource.png)
	
	L’opzione **Backup app** mostra tutti i backup creati direttamente dall'app stessa, perché questi sono gli unitici compatibili con le app. È possibile selezionare uno facilmente. L’opzione **Archiviazione** consente di selezionare il file ZIP del backup effettivo dall'account di archiviazione e dal contenitore configurato nel pannello **Backup**. Se sono presenti file di backup di eventuali altre app nel contenitore, è possibile selezionarli per il ripristino.

4. Quindi, specificare la destinazione per il ripristino dell’app in **Destinazione di ripristino**.

	![](./media/web-sites-restore/022ChooseDestination.png)
	
	>[AZURE.WARNING]Se si sceglie **Sovrascrivi**, tutti i dati relativi all'app esistente verranno cancellati. Prima di scegliere **OK**, assicurarsi che sia esattamente ciò che si desidera eseguire.
	
	È possibile selezionare **App esistente** per ripristinare il backup dell’app in un'altra applicazione nello stesso gruppo di risorse. Prima di utilizzare questa opzione, deve già essere stata creata un'altra app nel gruppo di risorse con mirroring della configurazione del database in quello definito nel backup dell’app.
	
5. Fare clic su **OK**.

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
	
1. Per visualizzare i dettagli sul successo o sulla mancata riuscita dell'operazione di ripristino dell'app, selezionare **Log di controllo** nel pannello **Sfoglia** principale. 
	
	Nel pannello **Log audio** vengono visualizzate tutte le operazioni, insieme ai dettagli relativi a livello, stato, risorsa e tempo.
	
2. Scorrere il pannello per individuare le operazioni correlate all'app.
3. Per visualizzare altri dettagli su un'operazione, selezionare tale operazione nell'elenco.
	
Nel pannello dei dettagli verranno visualizzate le informazioni disponibli correlate all'operazione.
	
>[AZURE.NOTE]Per iniziare a usare Servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

## Passaggi successivi

È inoltre possibile eseguire il backup e il ripristino delle applicazioni del servizio app mediante l'API REST (vedere [Usare REST per eseguire il backup e il ripristino di app del servizio App](websites-csm-backup.md)).

## Modifiche apportate
* Per una guida relativa al passaggio da Siti Web al servizio app, vedere [Servizio app di Azure e impatto sui servizi di Azure esistenti](http://go.microsoft.com/fwlink/?LinkId=529714)

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
 

<!---HONumber=AcomDC_0114_2016-->
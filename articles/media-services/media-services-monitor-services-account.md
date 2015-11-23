<properties 
	pageTitle="Monitorare un account di Servizi multimediali" 
	description="Spiega come configurare il monitoraggio per l'account di Servizi multimediali in Azure." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/07/2015"
	ms.author="juliako"/>



#<a id="monitormediaservicesaccount"></a>Come monitorare un account di Servizi multimediali

Nel dashboard di Servizi multimediali di Azure vengono presentate metriche di uso e informazioni relative all'account che è possibile usare per gestire il proprio account di Servizi multimediali.

You can monitor the number of queued encoding jobs, failed encoding tasks, active encoding jobs represented by the input and output data from the encoder, as well as the blob storage usage associated with your Media Services account. Se si trasferiscono informazioni in streaming ai clienti, è inoltre possibile recuperare varie metriche relative allo streaming. È possibile scegliere di monitorare i dati relativi alle ultime 6 o 24 ore oppure agli ultimi 7 giorni.
 
>[AZURE.NOTE]Al monitoraggio dei dati di archiviazione nel portale di gestione di Azure sono associati costi aggiuntivi. Per ulteriori informazioni, vedere [Analisi archiviazione e fatturazione](http://go.microsoft.com/fwlink/?LinkId=256667).

##<a id="configuremonitoring"></a>Procedura: Monitorare un account di Servizi multimediali

1. Nel [portale di gestione](http://go.microsoft.com/fwlink/?LinkID=256666) fare clic su **Media Services**, quindi sul nome dell'account di Servizi multimediali per aprire il dashboard. 

	![MediaServices\_Dashboard][dashboard]

2. Per monitorare i processi o i dati di codifica è sufficiente iniziare a inviare processi di codifica a Servizi multimediali, oppure iniziare a trasmettere contenuti in streaming ai clienti usando lo streaming on demand di contenuti multimediali di Azure. I dati di monitoraggio dovrebbero iniziare a essere visualizzati nel dashboard dopo circa un'ora.

##<a id="configuringstorage"></a>Procedura: monitorare l'utilizzo dell'archivio BLOB (facoltativo)
1. Fare clic sul nome dello **STORAGE ACCOUNT** nella sezione **quick glance**.
2. Nella pagina dell'account di archiviazione fare clic sul collegamento **configure page** e scorrere verso il basso fino alle impostazioni **monitoring** per i servizi BLOB, tabelle e accodamento, illustrati di seguito.

	>[AZURE.NOTE]I BLOB sono l'unico tipo di archiviazione supportata in Servizi multimediali.

	![StorageOptions][storage_options_scoped]

3. In **monitoring** impostare il livello di monitoraggio e i criteri di conservazione dei dati per i BLOB:

-  Per impostare il livello di monitoraggio, selezionare una delle opzioni seguenti:

      **Minimal**: consente di raccogliere metriche come ingresso/uscita, disponibilità, latenza e percentuali di successo aggregate per i servizi BLOB, tabelle e accodamento.

      **Verbose**: oltre alle metriche minime, consente di raccogliere lo stesso set di metriche per ogni operazione di archiviazione eseguita nell'API del servizio di archiviazione di Azure. Le metriche dettagliate consentono un'analisi più accurata dei problemi che si verificano durante l'elaborazione dell'applicazione.

      **Off**: consente di disattivare il monitoraggio. I dati di monitoraggio esistenti vengono mantenuti fino al termine del periodo di conservazione.

- Per impostare i criteri di conservazione dei dati, in **Retention (in days)** immettere il numero desiderato di giorni, da 1 a 365. Se non si desidera impostare criteri di conservazione, immettere zero. Se non vengono impostati criteri di conservazione, i dati di monitoraggio dovranno essere eliminati manualmente. È consigliabile impostare criteri di conservazione in base al periodo in cui si desidera conservare dati di analisi dell'archiviazione per l'account, in modo che i dati inutilizzati possano essere eliminati dal sistema senza costi aggiuntivi.

4. Dopo aver configurato il monitoraggio, fare clic su **Save**. Analogamente a quanto accade per le metriche di Servizi multimediali, i dati di monitoraggio dovrebbero iniziare a essere visualizzati nel dashboard dopo circa un'ora. Le metriche vengono archiviate nell'account di archiviazione in quattro tabelle denominate $MetricsTransactionsBlob, $MetricsTransactionsTable, $MetricsTransactionsQueue e $MetricsCapacityBlob. Per ulteriori informazioni, vedere [Informazioni sulle metriche di Analisi archiviazione](http://go.microsoft.com/fwlink/?LinkId=256668).



##Percorsi di apprendimento di Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fornire commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]flusso di lavoro Streaming su richiesta](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)


<!-- Images -->
[dashboard]: ./media/media-services-monitor-services-account/media-services-dashboard.png
[storage_options_scoped]: ./media/media-services-monitor-services-account/storagemonitoringoptions_scoped.png

 

<!---HONumber=Nov15_HO3-->
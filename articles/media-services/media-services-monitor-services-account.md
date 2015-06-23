<properties 
	pageTitle="Monitorare un account di Servizi multimediali - Azure" 
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
	ms.date="02/05/2015" 
	ms.author="juliako"/>





# <a id="monitormediaservicesaccount"></a>Come monitorare un account di Servizi multimediali

Questo articolo fa parte della serie [Flusso di lavoro Video On Demand di Servizi multimediali](media-services-video-on-demand-workflow.md) e [Flusso di lavoro Live Streaming di Servizi multimediali](media-services-live-streaming-workflow.md). 

Nel dashboard di Servizi multimediali di Azure vengono presentate metriche di uso e informazioni relative all'account che è possibile usare per gestire il proprio account di Servizi multimediali.

È possibile monitorare il numero di processi di codifica in coda, le attività di codifica non riuscite, i processi di codifica attivi rappresentati dai dati di input e output dal codificatore e l'utilizzo dell'archiviazione BLOB associato all'account di Servizi multimediali. Se si trasferiscono contenuti in streaming ai clienti, è inoltre possibile recuperare varie metriche relative allo streaming. È possibile scegliere di monitorare i dati relativi alle ultime 6 o 24 ore oppure agli ultimi 7 giorni.
 
>[AZURE.NOTE] Al monitoraggio dei dati di archiviazione nel portale di gestione di Azure sono associati costi aggiuntivi. Per altre informazioni, vedere [Analisi archiviazione e fatturazione](http://go.microsoft.com/fwlink/?LinkId=256667).

## <a id="configuremonitoring"></a>Procedura: Monitorare di un account di Media Services

1. Nel [portale di gestione](http://go.microsoft.com/fwlink/?LinkID=256666) fare clic su **Servizi multimediali**, quindi sul nome dell'account di Servizi multimediali per aprire il dashboard. 

	![MediaServices_Dashboard][dashboard]

2. Per monitorare i processi o i dati di codifica è sufficiente iniziare a inviare processi di codifica a Servizi multimediali, oppure iniziare a trasmettere contenuti in streaming ai clienti usando lo streaming on demand di contenuti multimediali di Azure. I dati di monitoraggio dovrebbero iniziare a essere visualizzati nel dashboard dopo circa un'ora.

## <a id="configuringstorage"></a>Procedura: Monitoraggio dell'uso dell'archiviazione BLOB (facoltativo)
1. Fare clic sul nome dell'**ACCOUNT DI ARCHIVIAZIONE 
** nella sezione **riepilogo rapido**.
2. Nella pagina dell'account di archiviazione fare clic sul collegamento **Configura** e scorrere verso il basso fino alle impostazioni **Monitoraggio** per i servizi BLOB, tabelle e accodamento, illustrati di seguito.

	>[AZURE.NOTE] I BLOB sono l'unico tipo di archiviazione supportata in Servizi multimediali.

	![StorageOptions][storage_options_scoped]

3. In **Monitoraggio** impostare il livello di monitoraggio e i criteri di conservazione dei dati per i BLOB:

-  Per impostare il livello di monitoraggio, selezionare una delle seguenti opzioni:

      **Minimo**: raccoglie le metriche come ingresso/uscita, disponibilità, latenza e percentuali di successo, aggregate per i servizi BLOB, tabelle e di accodamento.

      **Dettagliato**: oltre alle metriche minime, raccoglie lo stesso set di metriche per ogni operazione di archiviazione eseguita nell'API del servizio Archiviazione di Azure. Le metriche dettagliate consentono un'analisi più accurata dei problemi che si verificano durante l'elaborazione dell'applicazione. 

      **Disattivato**: disattiva il monitoraggio. I dati di monitoraggio esistenti vengono mantenuti fino al termine del periodo di conservazione.

- Per impostare i criteri di conservazione dei dati, in **Conservazione(in giorni)**, immettere il numero di giorni da 1 a 365. Se non si desidera impostare criteri di conservazione, immettere zero. Se non vengono impostati criteri di conservazione, i dati di monitoraggio dovranno essere eliminati manualmente. È consigliabile impostare criteri di conservazione in base al periodo in cui si desidera conservare dati di analisi dell'archiviazione per l'account, in modo che i dati inutilizzati possano essere eliminati dal sistema senza costi aggiuntivi.

4. Dopo aver configurato il monitoraggio, fare clic su **Salva**.
Analogamente a quanto accade per le metriche di Servizi multimediali, i dati di monitoraggio dovrebbero iniziare a essere visualizzati nel dashboard dopo circa un'ora.
Le metriche vengono archiviate nell'account di archiviazione in quattro tabelle denominate $MetricsTransactionsBlob, $MetricsTransactionsTable, $MetricsTransactionsQueue e $MetricsCapacityBlob. Per altre informazioni, vedere [Informazioni sulle metriche di Analisi archiviazione](http://go.microsoft.com/fwlink/?LinkId=256668).


<!-- Images -->
[dashboard]: ./media/media-services-monitor-services-account/media-services-dashboard.png
[storage_options_scoped]: ./media/media-services-monitor-services-account/storagemonitoringoptions_scoped.png



<!--HONumber=52--> 
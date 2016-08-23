<properties
	pageTitle="Come monitorare un account di archiviazione | Microsoft Azure"
	description="Informazioni su come monitorare un account di archiviazione in Azure usando il portale di Azure."
	services="storage"
	documentationCenter=""
	authors="robinsh"
	manager="carmonm"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/03/2016"
	ms.author="robinsh"/>  

# Monitorare un account di archiviazione nel portale di Azure

## Overview

È possibile monitorare l'account di archiviazione nel [portale di Azure](https://portal.azure.com). Quando si configura l'account di archiviazione per il monitoraggio attraverso il portale, Archiviazione di Azure usa [Analisi archiviazione](http://msdn.microsoft.com/library/azure/hh343270.aspx) per tenere traccia delle metriche relative all'account e registrare i dati delle richieste.

> [AZURE.NOTE] All'esame dei dati di monitoraggio nel [portale di Azure](https://portal.azure.com) sono associati costi aggiuntivi. Per ulteriori informazioni, vedere <a href="http://msdn.microsoft.com/library/azure/hh360997.aspx">Analisi archiviazione e fatturazione</a>. <br />

> L’archiviazione file di Azure attualmente supporta la metrica di analisi di archiviazione, ma non supporta ancora l'accesso. È possibile abilitare la metrica per l'archiviazione file di Azure tramite il [portale di Azure](https://portal.azure.com).

> Per gli account di archiviazione con un tipo di replica di archiviazione con ridondanza della zona (ZRS) al momento non sono abilitate le funzionalità di metrica e registrazione.

> Per una guida dettagliata sull'utilizzo di Analisi archiviazione e di altri strumenti per identificare, diagnosticare e risolvere i problemi relativi ad Archiviazione di Azure, vedere [Monitoraggio, diagnosi e risoluzione dei problemi del servizio di archiviazione di Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md).


## Procedura: Configurare il monitoraggio per un account di archiviazione

1. Nel [portale di Azure](https://portal.azure.com) fare clic su **Storage** e quindi sul nome dell'account di archiviazione per aprire il dashboard.

2. Fare clic su **Configure** e scorrere verso il basso fino alle impostazioni di **monitoraggio** per i servizi BLOB, tabelle e accodamento.

	![MonitoringOptions](./media/storage-monitor-storage-account/Storage_MonitoringOptions.png)

3. In **monitoring** impostare il livello di monitoraggio e i criteri di conservazione dei dati per ogni servizio:

	-  Per impostare il livello di monitoraggio, selezionare una delle opzioni seguenti:

      **Minimal**: consente di raccogliere metriche come ingresso/uscita, disponibilità, latenza e percentuali di successo aggregate per i servizi BLOB, tabelle e accodamento.

      **Verbose**: oltre alle metriche minime, consente di raccogliere lo stesso set di metriche per ogni operazione di archiviazione eseguita nell'API del servizio di archiviazione di Azure. Le metriche dettagliate consentono un'analisi più accurata dei problemi che si verificano durante l'elaborazione dell'applicazione.

      **Off**: consente di disattivare il monitoraggio. I dati di monitoraggio esistenti vengono mantenuti fino al termine del periodo di conservazione.

- Per impostare i criteri di conservazione dei dati, in **Retention (in days)** immettere il numero desiderato di giorni, da 1 a 365. Se non si desidera impostare criteri di conservazione, immettere zero. Se non vengono impostati criteri di conservazione, i dati di monitoraggio dovranno essere eliminati manualmente. È consigliabile impostare criteri di conservazione in base al periodo in cui si desidera conservare dati di analisi dell'archiviazione per l'account, in modo che i dati inutilizzati possano essere eliminati dal sistema senza costi aggiuntivi.

4. Dopo aver configurato il monitoraggio, fare clic su **Save**.

I dati di monitoraggio dovrebbero iniziare a essere visualizzati nel dashboard e nella pagina **Monitor** dopo circa un'ora.

Finché non viene configurato il monitoraggio per un account di archiviazione, i relativi dati non verranno raccolti e i grafici delle metriche saranno vuoti nel dashboard e nella pagina **Monitor**.

Dopo aver impostato i livelli di monitoraggio e i criteri di conservazione, è possibile scegliere quali metriche monitorare nel [portale di Azure](https://portal.azure.com) e quali tracciare sui grafici delle metriche. Per ogni livello di monitoraggio viene visualizzato un set predefinito di metriche. È possibile utilizzare **Add Metrics** per aggiungere o rimuovere metriche dall'elenco.

Le metriche vengono archiviate nell'account di archiviazione in quattro tabelle denominate $MetricsTransactionsBlob, $MetricsTransactionsTable, $MetricsTransactionsQueue e $MetricsCapacityBlob. Per ulteriori informazioni, vedere [Informazioni sulle metriche di Analisi archiviazione](http://msdn.microsoft.com/library/azure/hh343258.aspx).


## Procedura: Personalizzare la dashboard per il monitoraggio

Nel dashboard è possibile scegliere fino a sei metriche da tracciare sul grafico tra le nove disponibili. Per ogni servizio (BLOB, tabelle e accodamento) sono disponibili le metriche Availability, Success Percentage e Total Requests. Le metriche disponibili nel dashboard sono identiche per il monitoraggio minimo o dettagliato.

1. Nel [portale di Azure](https://portal.azure.com) fare clic su **Archiviazione** e quindi sul nome dell'account di archiviazione per aprire il dashboard.

2. Per cambiare le metriche da tracciare sul grafico, eseguire una delle operazioni seguenti:

	- Per aggiungere una nuova metrica al grafico, fare clic sulla casella di controllo colorata accanto alla relativa intestazione nella tabella sotto il grafico.

	- Per nascondere una metrica tracciata sul grafico, deselezionare la casella di controllo colorata accanto alla relativa intestazione.

		![Monitoring\_nmore](./media/storage-monitor-storage-account/storage_Monitoring_nmore.png)

3. Per impostazione predefinita, nel grafico vengono mostrate le tendenze e per ogni metrica viene visualizzato solo il valore corrente, ossia l'opzione **Relative** nella parte superiore del grafico. Per visualizzare un asse Y in modo da vedere i valori assoluti, selezionare **Absolute**.

4. Per modificare l'intervallo di tempo visualizzato sul grafico delle metriche, selezionare 6 ore, 24 ore, o 7 giorni nella parte superiore del grafico.


## Procedura: Personalizzare la pagina Monitor

Nella pagina **Monitor** è possibile visualizzare il set completo di metriche dell'account di archiviazione.

- Se per l'account di archiviazione è configurato il monitoraggio minimo, le metriche come ingresso/uscita, disponibilità, latenza e percentuali di successo sono aggregate per i servizi BLOB, tabelle e accodamento.

- Se invece è configurato il monitoraggio dettagliato, le metriche sono disponibili con una maggiore risoluzione in base a singole operazioni di archiviazione, oltre a quelle aggregate a livello di servizio.

Eseguire le procedure seguenti per scegliere quali metriche di archiviazione visualizzare nel grafico e nella tabella presenti nella pagina **Monitor**. Queste impostazioni non influiscono sulle operazioni di raccolta, aggregazione e archiviazione dei dati di monitoraggio nell'account di archiviazione.

## Procedura: Aggiungere metriche alla relativa tabella


1. Nel [portale di Azure](https://portal.azure.com) fare clic su **Archiviazione** e quindi sul nome dell'account di archiviazione per aprire il dashboard.

2. Fare clic su **Monitor**.

	Verrà visualizzata la pagina **Monitor**. Per impostazione predefinita, nella tabella viene visualizzato un subset delle metriche disponibili per il monitoraggio. Nella figura è illustrata la visualizzazione predefinita di Monitor per un account di archiviazione con il monitoraggio dettagliato configurato per tutti e tre i servizi. Utilizzare **Add Metrics** per selezionare le metriche da monitorare tra quelle disponibili.

	![Monitoring\_VerboseDisplay](./media/storage-monitor-storage-account/Storage_Monitoring_VerboseDisplay.png)

	> [AZURE.NOTE] Valutare i costi quando si selezionano le metriche. Sono previsti costi di transazione e di uscita associati all'aggiornamento delle visualizzazioni del monitoraggio. Per altre informazioni, vedere [Analisi archiviazione e fatturazione](http://msdn.microsoft.com/library/azure/hh360997.aspx).

3. Fare clic su **Add Metrics**.

	Le metriche aggregate disponibili con il monitoraggio minimo vengono visualizzate all'inizio dell'elenco. Se la casella di controllo è selezionata, le metriche vengono visualizzate nell'elenco.

	![AddMetricsInitialDisplay](./media/storage-monitor-storage-account/Storage_AddMetrics_InitialDisplay.png)

4. Passare il puntatore sul lato destro della finestra di dialogo per visualizzare una barra di scorrimento che è possibile trascinare per accedere a metriche aggiuntive.

	![AddMetricsScrollbar](./media/storage-monitor-storage-account/Storage_AddMetrics_Scrollbar.png)


5. Fare clic sulla freccia verso il basso accanto a una metrica per espandere un elenco di operazioni incluse nel relativo ambito. Selezionare ogni operazione che si vuole visualizzare nella tabella delle metriche nel [portale di Azure](https://portal.azure.com).

	Nella figura seguente è stata espansa la metrica PERCENTUALE DI ERRORI DI AUTORIZZAZIONE.

	![ExpandCollapse](./media/storage-monitor-storage-account/Storage_AddMetrics_ExpandCollapse.png)


6. Dopo aver selezionato le metriche per tutti i servizi, fare clic su OK (segno di spunta) per aggiornare la configurazione del monitoraggio. Le metriche selezionate vengono aggiunte alla tabella.

7. Per eliminare una metrica dalla tabella, fare clic sulla metrica per selezionarla e quindi fare clic su **Delete Metric**.

	![DeleteMetric](./media/storage-monitor-storage-account/Storage_DeleteMetric.png)

## Procedura: Personalizzare il grafico delle metriche nella pagina Monitor

1. Nella pagina **Monitor** relativa all'account di archiviazione selezionare fine a 6 metriche nella tabella da tracciare sul grafico. Per selezionare una metrica, fare clic sulla casella di controllo a sinistra. Per rimuovere una metrica dal grafico, deselezionarla.

2. Per passare tra la visualizzazione di valori relativi (solo il valore finale) e valori assoluti (asse Y), selezionare **Relative** o **Absolute** nella parte superiore del grafico.

3.	Per modificare l'intervallo di tempo visualizzato sul grafico delle metriche, selezionare **6 ore**, **24 ore** o **7 giorni** nella parte superiore del grafico.



## Procedura: Configurare la registrazione

Per ogni servizio di archiviazione disponibile con l'account di archiviazione (BLOB, tabelle e accodamento) è possibile salvare log di diagnostica per le richieste di lettura, di scrittura e/o di eliminazione, nonché impostare i singoli criteri di conservazione dei dati.

1. Nel [portale di Azure](https://portal.azure.com) fare clic su **Archiviazione** e quindi sul nome dell'account di archiviazione per aprire il dashboard.

2. Fare clic su **Configure** e usare il tasto freccia GIÙ per scorrere fino a **logging**.

	![Storagelogging](./media/storage-monitor-storage-account/Storage_LoggingOptions.png)


3. Per ogni servizio (BLOB, tabelle e accodamento) configurare le opzioni seguenti:

	- Tipi di richiesta da registrare, ovvero di lettura, scrittura ed eliminazione.

	- Numero di giorni di conservazione dei dati registrati. Se non si desidera impostare criteri di conservazione, immettere zero. Se non vengono impostati criteri di conservazione, i log dovranno essere eliminati manualmente.

4. Fare clic su **Save**.

I log di diagnostica vengono salvati in un contenitore BLOB denominato $logs nell'account di archiviazione. Per informazioni sull'accesso al contenitore $logs, vedere [Informazioni sulla registrazione di Analisi archiviazione](http://msdn.microsoft.com/library/azure/hh343262.aspx).

<!---HONumber=AcomDC_0810_2016-->
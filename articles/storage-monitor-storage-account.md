<properties urlDisplayName="How to monitor" pageTitle="Come monitorare un account di archiviazione | Microsoft Azure" metaKeywords="Azure monitor storage accounts, storage account management portal, storage account dashboard, storage metrics table, storage metrics chart" description="Learn how to monitor a storage account in Azure by using the Management Portal." metaCanonical="" services="storage" documentationCenter="" title="How To Monitor a Storage Account" authors="tamram" solutions="" manager="adinah" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/17/2014" ms.author="tamram" />

# <a id="createstorageaccount"></a>Monitorare un account di archiviazione nel portale di gestione di Azure

È possibile monitorare l'account di archiviazione nel portale di gestione di Azure. Quando si configura l'account di archiviazione per il monitoraggio attraverso il portale, Archiviazione di Azure usa [Analisi archiviazione](http://msdn.microsoft.com/it-it/library/azure/hh343270.aspx) per tenere traccia delle metriche relative all'account e registrare i dati delle richieste. 

> [WACOM.NOTE] All'esame dei dati di monitoraggio nel portale di gestione sono associati costi aggiuntivi. Per altre informazioni, vedere <a href="http://msdn.microsoft.com/it-it/library/windowsazure/hh360997.aspx">Analisi archiviazione e fatturazione</a>. 

> [WACOM.NOTE] Il servizio File di Azure attualmente non supporta Analisi archiviazione.

> [WACOM.NOTE] Per una guida approfondita sull'uso di Analisi archiviazione e di altri strumenti per identificare, diagnosticare e risolvere i problemi correlati ad Archiviazione di Azure, vedere [Monitoraggio, diagnosi e risoluzione dei problemi del servizio di archiviazione Microsoft Azure].(../storage-monitoring-diagnosing-troubleshooting/).


##Sommario##

* [Procedura: Configurare il monitoraggio per un account di archiviazione](#configurestoragemonitoring)
* [Procedura: Personalizzare il dashboard per il monitoraggio](#customizestoragemonitoring)
* [Procedura: Personalizzare la pagina Monitor](#customizemonitorpage)
* [Procedura: Aggiungere metriche alla relativa tabella](#addmonitoringmetrics)
* [Procedura: Personalizzare il grafico delle metriche nella pagina Monitor](#customizemetricschart)
* [Procedura: Configurare la registrazione](#configurelogging)


<h2><a id="configurestoragemonitoring"></a>Procedura: Configurare il monitoraggio per un account di archiviazione</h2>

1. Nel [portale di gestione](https://manage.windowsazure.com/) fare clic su **Archiviazione** e quindi sul nome dell'account di archiviazione per aprire il dashboard.

2. Fare clic su **Configura** e scorrere la pagina verso il basso fino alle impostazioni di **Monitoraggio** per i servizi BLOB, tabelle e di accodamento, mostrate di seguito.

	![MonitoringOptions](./media/storage-monitor-storage-account/Storage_MonitoringOptions.png)

3. In **Monitoraggio** impostare il livello di monitoraggio e i criteri di conservazione dei dati per ogni servizio:

-  Per impostare il livello di monitoraggio, selezionare una delle opzioni seguenti:

      **Minimo**: raccoglie le metriche come ingresso/uscita, disponibilità, latenza e percentuali di successo, aggregate per i servizi BLOB, tabelle e di accodamento.

      **Dettagliato**: oltre alle metriche minime, raccoglie lo stesso set di metriche per ogni operazione di archiviazione eseguita nell'API del servizio Archiviazione di Azure. Le metriche dettagliate consentono un'analisi più accurata dei problemi che si verificano durante l'elaborazione dell'applicazione. 

      **Disattivato**: disattiva il monitoraggio. I dati di monitoraggio esistenti vengono mantenuti fino al termine del periodo di conservazione.

- Per impostare i criteri di conservazione dei dati, in **Conservazione (in giorni)** immettere il numero di giorni, da 1 a 365. Se non si desidera impostare criteri di conservazione, immettere zero. Se non vengono impostati criteri di conservazione, i dati di monitoraggio dovranno essere eliminati manualmente. È consigliabile impostare criteri di conservazione in base al periodo in cui si desidera conservare dati di analisi dell'archiviazione per l'account, in modo che i dati inutilizzati possano essere eliminati dal sistema senza costi aggiuntivi.

4. Dopo aver configurato il monitoraggio, fare clic su **Salva**.

I dati di monitoraggio dovrebbero iniziare a essere visualizzati nel dashboard e nella pagina **Monitoraggio** dopo circa un'ora.

Finché non viene configurato il monitoraggio per un account di archiviazione, non viene raccolto alcun dato di monitoraggio e i grafici delle metriche sono vuoti nel dashboard e nella pagina **Monitoraggio**.

Dopo aver impostato i livelli di monitoraggio e i criteri di conservazione, è possibile scegliere quali metriche monitorare nel portale di gestione e quali tracciare sui grafici delle metriche. Per ogni livello di monitoraggio viene visualizzato un set predefinito di metriche. È possibile usare **Aggiungi metriche** per aggiungere o rimuovere metriche nell'elenco.

Le metriche vengono archiviate nell'account di archiviazione in quattro tabelle denominate $MetricsTransactionsBlob, $MetricsTransactionsTable, $MetricsTransactionsQueue e $MetricsCapacityBlob. Per altre informazioni, vedere [Informazioni sulle metriche di Analisi archiviazione](http://msdn.microsoft.com/it-it/library/windowsazure/hh343258.aspx).


<h2><a id="customizestoragemonitoring"></a>Procedura: Personalizzare il dashboard per il monitoraggio</h2>

Nel dashboard è possibile scegliere fino a sei metriche da tracciare sul grafico tra le nove disponibili. Per ogni servizio (BLOB, tabelle e accodamento), sono disponibili le metriche Availability, Success Percentage e Total Requests. Le metriche disponibili nel dashboard sono identiche per il monitoraggio minimo o dettagliato.

1. Nel [portale di gestione](https://manage.windowsazure.com/) fare clic su **Archiviazione** e quindi sul nome dell'account di archiviazione per aprire il dashboard.

2. Per cambiare le metriche da tracciare sul grafico, eseguire una delle operazioni seguenti:

- Per aggiungere una nuova metrica al grafico, fare clic sulla casella di controllo accanto alla relativa intestazione. In una visualizzazione ridotta fare clic su **altre *n*** per accedere alle intestazioni che non possono essere visualizzate nell'area.

- Per nascondere una metrica tracciata sul grafico, deselezionare la casella di controllo accanto alla relativa intestazione.

	![Monitoring_nmore](./media/storage-monitor-storage-account/storage_Monitoring_nmore.png)
  
3. Per impostazione predefinita, il grafico mostra le tendenze, visualizzando solo il valore corrente per ogni metrica, che corrisponde all'opzione **Relativo** nella parte superiore del grafico. Per visualizzare un asse Y in modo da leggere i valori assoluti, selezionare **Assoluto**.

4. Per modificare l'intervallo di tempo visualizzato sul grafico delle metriche, selezionare 6 ore, 24 ore o 7 giorni nella parte superiore del grafico.
     

<h2><a id="customizemonitorpage"></a>Procedura: Personalizzare la pagina Monitor</h2>

Nella pagina **Monitoraggio** è possibile visualizzare il set completo di metriche per l'account di archiviazione. 

- Se per l'account di archiviazione è configurato il monitoraggio minimo, le metriche come ingresso/uscita, disponibilità, latenza e percentuali di successo sono aggregate per i servizi BLOB, tabelle e accodamento.

- Se invece è configurato il monitoraggio dettagliato, le metriche sono disponibili con una maggiore risoluzione in base a singole operazioni di archiviazione, oltre a quelle aggregate a livello di servizio.

Usare le procedure seguenti per scegliere le metriche di archiviazione da visualizzare nel grafico e nella tabella all'interno della pagina **Monitoraggio**. Queste impostazioni non influiscono sulle operazioni di raccolta, aggregazione e archiviazione dei dati di monitoraggio nell'account di archiviazione.

<h2><a id="addmonitoringmetrics"></a>Procedura: Aggiungere metriche alla relativa tabella</h2>


1. Nel [portale di gestione](https://manage.windowsazure.com/) fare clic su **Archiviazione** e quindi sul nome dell'account di archiviazione per aprire il dashboard.

2. Fare clic su **Monitoraggio**.

	Verrà visualizzata la pagina **Monitoraggio**. Per impostazione predefinita, nella tabella viene visualizzato un subset delle metriche disponibili per il monitoraggio. Nella figura è illustrata la visualizzazione predefinita di Monitor per un account di archiviazione con il monitoraggio dettagliato configurato per tutti e tre i servizi. Usare **Aggiungi metriche** per selezionare le metriche che si vuole monitorare tra tutte quelle disponibili.


	![Monitoring_VerboseDisplay](./media/storage-monitor-storage-account/Storage_Monitoring_VerboseDisplay.png)

	<div class="dev-callout"> 
	<b>Nota</b> 
	<p>Valutare i costi quando si selezionano le metriche. Sono previsti costi di transazione e di uscita associati all'aggiornamento delle visualizzazioni del monitoraggio. Per altre informazioni, vedere <a href="http://msdn.microsoft.com/it-it/library/windowsazure/hh360997.aspx">Analisi archiviazione e fatturazione</a>.</p> 
</div>

3. Fare clic su **Aggiungi metriche**. 

	Le metriche aggregate disponibili con il monitoraggio minimo vengono visualizzate all'inizio dell'elenco. Se la casella di controllo è selezionata, le metriche vengono visualizzate nell'elenco. 

	![AddMetricsInitialDisplay](./media/storage-monitor-storage-account/Storage_AddMetrics_InitialDisplay.png)
 
4. Passare il puntatore sul lato destro della finestra di dialogo per visualizzare una barra di scorrimento che è possibile trascinare per accedere a metriche aggiuntive.

	![AddMetricsScrollbar](./media/storage-monitor-storage-account/Storage_AddMetrics_Scrollbar.png)


5. Fare clic sulla freccia verso il basso accanto a una metrica per espandere un elenco di operazioni incluse nel relativo ambito. Selezionare ogni operazione che si desidera visualizzare nella tabella delle metriche nel portale di gestione.

	Nella figura seguente è stata espansa la metrica PERCENTUALE DI ERRORI DI AUTORIZZAZIONE.

	![ExpandCollapse](./media/storage-monitor-storage-account/Storage_AddMetrics_ExpandCollapse.png)


6. Dopo aver selezionato le metriche per tutti i servizi, fare clic su OK (segno di spunta) per aggiornare la configurazione del monitoraggio. Le metriche selezionate vengono aggiunte alla tabella.

7. Per eliminare una metrica dalla tabella, fare clic sulla metrica per selezionarla e quindi fare clic su **Elimina metrica**, come mostrato di seguito.

	![DeleteMetric](./media/storage-monitor-storage-account/Storage_DeleteMetric.png)

<h2><a id="customizemetricschart"></a>Procedura: Personalizzare il grafico delle metriche nella pagina Monitor</h2>

1. Nella tabella delle metriche nella pagina **Monitoraggio** per l'account di archiviazione selezionare fino a 6 metriche da tracciare sul grafico. Per selezionare una metrica, fare clic sulla casella di controllo a sinistra. Per rimuovere una metrica dal grafico, deselezionarla.

2. Per passare tra la visualizzazione di valori relativi (solo il valore finale) e valori assoluti (asse Y visualizzato), selezionare **Relativo** o **Assoluto** nella parte superiore del grafico.

3.	Per modificare l'intervallo di tempo visualizzato sul grafico delle metriche, selezionare **6 ore**, **24 ore**, o **7 giorni** nella parte superiore del grafico.



<h2><a id="configurelogging"></a>Procedura: Configurare la registrazione</h2>

Per ogni servizio di archiviazione disponibile con l'account di archiviazione (BLOB, tabelle e accodamento), è possibile salvare log di diagnostica per le richieste di lettura, di scrittura e/o di eliminazione, nonché impostare i singoli criteri di conservazione dei dati.

1. Nel [portale di gestione](https://manage.windowsazure.com/) fare clic su **Archiviazione** e quindi sul nome dell'account di archiviazione per aprire il dashboard.

2. Fare clic su **Configura** e premere freccia GIÙ sulla tastiera per scorrere verso il basso fino a **Registrazione** (mostrato di seguito).

	![Storagelogging](./media/storage-monitor-storage-account/Storage_LoggingOptions.png)

 
3. Per ogni servizio (BLOB, tabelle e accodamento), configurare le opzioni seguenti:

	- Tipi di richieste da registrare, ovvero di lettura, scrittura ed eliminazione

	- Numero di giorni di conservazione dei dati registrati. Se non si desidera impostare criteri di conservazione, immettere zero. Se non vengono impostati criteri di conservazione, i log dovranno essere eliminati manualmente.

4. Fare clic su **Salva**.

I log di diagnostica vengono salvati in un contenitore BLOB denominato $logs nell'account di archiviazione. Per informazioni sull'accesso al contenitore $logs, vedere [Informazioni sulla registrazione di Analisi archiviazione](http://msdn.microsoft.com/it-it/library/windowsazure/hh343262.aspx).

<!--HONumber=35_1-->

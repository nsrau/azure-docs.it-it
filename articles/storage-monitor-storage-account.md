<properties linkid="manage-services-how-to-monitor-a-storage-account" urlDisplayName="How to monitor" pageTitle="How to monitor a storage account | Microsoft Azure" metaKeywords="Azure monitor storage accounts, storage account management portal, storage account dashboard, storage metrics table, storage metrics chart" description="Learn how to monitor a storage account in Azure by using the Management Portal." metaCanonical="" services="storage" documentationCenter="" title="How To Monitor a Storage Account" authors="tamram" solutions="" manager="mbaldwin" editor="cgronlun" />

Come monitorare un account di archiviazione
===========================================

È possibile monitorare gli account di archiviazione nel portale di gestione in anteprima di Azure. Per ogni servizio di archiviazione associato all'account di archiviazione (BLOB, accodamento e tabelle), è possibile scegliere il livello di monitoraggio, ossia minimo o dettagliato, e specificare i criteri appropriati per la conservazione dei dati.

Finché non viene configurato il monitoraggio per un account di archiviazione, i relativi dati non verranno raccolti e i grafici delle metriche saranno vuoti nel dashboard e nella pagina **Monitor**.

**Nota**

All'esame dei dati di monitoraggio nel portale di gestione sono associati costi aggiuntivi. Per ulteriori informazioni, vedere [Analisi archiviazione e fatturazione](http://msdn.microsoft.com/it-it/library/windowsazure/hh360997.aspx).

Sommario
--------

-   [Procedura: Configurare il monitoraggio per un account di archiviazione](#configurestoragemonitoring)
-   [Procedura: Personalizzare il dashboard per il monitoraggio](#customizestoragemonitoring)
-   [Procedura: Personalizzare la pagina Monitor](#customizemonitorpage)
-   [Procedura: Aggiungere metriche alla relativa tabella](#addmonitoringmetrics)
-   [Procedura: Personalizzare il grafico delle metriche nella pagina Monitor](#customizemetricschart)
-   [Procedura: Configurare la registrazione](#configurelogging)

Procedura: Configurare il monitoraggio per un account di archiviazione
----------------------------------------------------------------------

1.  Nel [portale di gestione](https://manage.windowsazure.com/) fare clic su **Storage** e quindi sul nome dell'account di archiviazione per aprire il dashboard.

2.  Fare clic su **Configure** e scorrere verso il basso fino alle impostazioni di **monitoraggio** per i servizi BLOB, tabelle e accodamento, illustrati di seguito.

    ![MonitoringOptions](./media/storage-monitor-storage-account/Storage_MonitoringOptions.png)

3.  In **monitoring** impostare il livello di monitoraggio e i criteri di conservazione dei dati per ogni servizio:

-   Per impostare il livello di monitoraggio, selezionare una delle opzioni seguenti:

    **Minimal**: consente di raccogliere metriche come ingresso/uscita, disponibilità, latenza e percentuali di successo aggregate per i servizi BLOB, tabelle e accodamento.

    **Verbose**: oltre alle metriche minime, consente di raccogliere lo stesso set di metriche per ogni operazione di archiviazione eseguita nell'API del servizio di archiviazione di Azure. Le metriche dettagliate consentono un'analisi più accurata dei problemi che si verificano durante l'elaborazione dell'applicazione.

    **Off**: consente di disattivare il monitoraggio. I dati di monitoraggio esistenti vengono mantenuti fino al termine del periodo di conservazione.

-   Per impostare i criteri di conservazione dei dati, in **Retention (in days)** immettere il numero desiderato di giorni, da 1 a 365. Se non si desidera impostare criteri di conservazione, immettere zero. Se non vengono impostati criteri di conservazione, i dati di monitoraggio dovranno essere eliminati manualmente. È consigliabile impostare criteri di conservazione in base al periodo in cui si desidera conservare dati di analisi dell'archiviazione per l'account, in modo che i dati inutilizzati possano essere eliminati dal sistema senza costi aggiuntivi.

1.  Dopo aver configurato il monitoraggio, fare clic su **Save**.

I dati di monitoraggio dovrebbero iniziare a essere visualizzati nel dashboard e nella pagina **Monitor** dopo circa un'ora.

Le metriche vengono archiviate nell'account di archiviazione in quattro tabelle denominate \$MetricsTransactionsBlob, \$MetricsTransactionsTable, \$MetricsTransactionsQueue e \$MetricsCapacityBlob. Per ulteriori informazioni, vedere [Informazioni sulle metriche di Analisi archiviazione](http://msdn.microsoft.com/it-it/library/windowsazure/hh343258.aspx).

Dopo aver impostato i livelli di monitoraggio e i criteri di conservazione, è possibile scegliere quali metriche monitorare nel portale di gestione e quali tracciare sui grafici delle metriche. Per ogni livello di monitoraggio viene visualizzato un set predefinito di metriche. È possibile utilizzare **Add Metrics** per aggiungere o rimuovere metriche dall'elenco.

Procedura: Personalizzare il dashboard per il monitoraggio
----------------------------------------------------------

Nel dashboard è possibile scegliere fino a sei metriche da tracciare sul grafico tra le nove disponibili. Per ogni servizio (BLOB, tabelle e accodamento), sono disponibili le metriche Availability, Success Percentage e Total Requests. Le metriche disponibili nel dashboard sono identiche per il monitoraggio minimo o dettagliato.

1.  Nel [portale di gestione](https://manage.windowsazure.com/) fare clic su **Storage** e quindi sul nome dell'account di archiviazione per aprire il dashboard.

2.  Per cambiare le metriche da tracciare sul grafico, eseguire una delle operazioni seguenti:

-   Per aggiungere una nuova metrica al grafico, fare clic sulla casella di controllo accanto alla relativa intestazione. In una visualizzazione ridotta fare clic su ***n* more** per accedere alle intestazioni non visualizzate nell'area.

-   Per nascondere una metrica tracciata sul grafico, deselezionare la casella di controllo accanto alla relativa intestazione.

    ![Monitoring\_nmore](./media/storage-monitor-storage-account/storage_Monitoring_nmore.png)

1.  Per impostazione predefinita, nel grafico vengono mostrate le tendenze e per ogni metrica viene visualizzato solo il valore corrente, ossia l'opzione **Relative** nella parte superiore del grafico. Per visualizzare un asse Y in modo da vedere i valori assoluti, selezionare **Absolute**.

2.  Per modificare l'intervallo di tempo visualizzato sul grafico delle metriche, selezionare 6 ore, 24 ore o 7 giorni nella parte superiore del grafico.

Procedura: Personalizzare la pagina Monitor
-------------------------------------------

Nella pagina **Monitor** è possibile visualizzare il set completo di metriche dell'account di archiviazione.

-   Se per l'account di archiviazione è configurato il monitoraggio minimo, le metriche come ingresso/uscita, disponibilità, latenza e percentuali di successo sono aggregate per i servizi BLOB, tabelle e accodamento.

-   Se invece è configurato il monitoraggio dettagliato, le metriche sono disponibili con una maggiore risoluzione in base a singole operazioni di archiviazione, oltre a quelle aggregate a livello di servizio.

Eseguire le procedure seguenti per scegliere quali metriche di archiviazione visualizzare nel grafico e nella tabella presenti nella pagina **Monitor**. Queste impostazioni non influiscono sulle operazioni di raccolta, aggregazione e archiviazione dei dati di monitoraggio nell'account di archiviazione.

Procedura: Aggiungere metriche alla relativa tabella
----------------------------------------------------

1.  Nel [portale di gestione](https://manage.windowsazure.com/) fare clic su **Storage** e quindi sul nome dell'account di archiviazione per aprire il dashboard.

2.  Fare clic su **Monitor**.

    Verrà visualizzata la pagina **Monitor**. Per impostazione predefinita, nella tabella viene visualizzato un subset delle metriche disponibili per il monitoraggio. Nella figura è illustrata la visualizzazione predefinita di Monitor per un account di archiviazione con il monitoraggio dettagliato configurato per tutti e tre i servizi. Utilizzare **Add Metrics** per selezionare le metriche da monitorare tra quelle disponibili.

    ![Monitoring\_VerboseDisplay](./media/storage-monitor-storage-account/Storage_Monitoring_VerboseDisplay.png)

    **Nota**

    Valutare i costi quando si selezionano le metriche. Sono previsti costi di transazione e di uscita associati all'aggiornamento delle visualizzazioni del monitoraggio. Per ulteriori informazioni, vedere [Analisi archiviazione e fatturazione](http://msdn.microsoft.com/it-it/library/windowsazure/hh360997.aspx).

	

1.  Fare clic su **Add Metrics**.

    Le metriche aggregate disponibili con il monitoraggio minimo vengono visualizzate all'inizio dell'elenco. Se la casella di controllo è selezionata, le metriche vengono visualizzate nell'elenco.

    ![AddMetricsInitialDisplay](./media/storage-monitor-storage-account/Storage_AddMetrics_InitialDisplay.png)

2.  Passare il puntatore sul lato destro della finestra di dialogo per visualizzare una barra di scorrimento che è possibile trascinare per accedere a metriche aggiuntive.

    ![AddMetricsScrollbar](./media/storage-monitor-storage-account/Storage_AddMetrics_Scrollbar.png)

3.  Fare clic sulla freccia verso il basso accanto a una metrica per espandere un elenco di operazioni incluse nel relativo ambito. Selezionare ogni operazione che si desidera visualizzare nella tabella delle metriche nel portale di gestione.

    Nella figura seguente è stata espansa la metrica AUTHORIZATION ERROR PERCENTAGE.

    ![ExpandCollapse](./media/storage-monitor-storage-account/Storage_AddMetrics_ExpandCollapse.png)

4.  Dopo aver selezionato le metriche per tutti i servizi, fare clic su OK (segno di spunta) per aggiornare la configurazione del monitoraggio. Le metriche selezionate vengono aggiunte alla tabella.

5.  Per eliminare una metrica dalla tabella, fare clic sulla metrica per selezionarla e quindi fare clic su **Delete Metric**, come illustrato di seguito.

    ![DeleteMetric](./media/storage-monitor-storage-account/Storage_DeleteMetric.png)

Procedura: Personalizzare il grafico delle metriche nella pagina Monitor
------------------------------------------------------------------------

1.  Nella pagina **Monitor** relativa all'account di archiviazione selezionare fine a 6 metriche nella tabella da tracciare sul grafico. Per selezionare una metrica, fare clic sulla casella di controllo a sinistra. Per rimuovere una metrica dal grafico, deselezionarla.

2.  Per passare tra la visualizzazione di valori relativi (solo il valore finale) e valori assoluti (asse Y), selezionare **Relative** o **Absolute** nella parte superiore del grafico.

3.  Per modificare l'intervallo di tempo visualizzato sul grafico delle metriche, selezionare **6 ore**, **24 ore** o **7 giorni** nella parte superiore del grafico.

Procedura: Configurare la registrazione
---------------------------------------

Per ogni servizio di archiviazione disponibile con l'account di archiviazione (BLOB, tabelle e accodamento), è possibile salvare log di diagnostica per le richieste di lettura, di scrittura e/o di eliminazione, nonché impostare i singoli criteri di conservazione dei dati.

1.  Nel [portale di gestione](https://manage.windowsazure.com/) fare clic su **Storage** e quindi sul nome dell'account di archiviazione per aprire il dashboard.

2.  Fare clic su **Configure** e utilizzare il tasto freccia GIÙ per scorrere fino a **logging**, come illustrato di seguito.

    ![Storagelogging](./media/storage-monitor-storage-account/Storage_LoggingOptions.png)

3.  Per ogni servizio (BLOB, tabelle e accodamento), configurare le opzioni seguenti:

    -   Tipi di richieste da registrare, ovvero di lettura, scrittura ed eliminazione

    -   Numero di giorni di conservazione dei dati registrati. Se non si desidera impostare criteri di conservazione, immettere zero. Se non vengono impostati criteri di conservazione, i log dovranno essere eliminati manualmente.

4.  Fare clic su **Save**.

I log di diagnostica vengono salvati in un contenitore BLOB denominato \$logs nell'account di archiviazione. Per informazioni sull'accesso al contenitore \$logs, vedere [Informazioni sulla registrazione di Analisi archiviazione](http://msdn.microsoft.com/it-it/library/windowsazure/hh343262.aspx).


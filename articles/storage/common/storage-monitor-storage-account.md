---
title: Come monitorare un account di archiviazione di Azure | Microsoft Docs
description: Informazioni su come monitorare un account di archiviazione in Azure usando il portale di Azure.
services: storage
documentationcenter: 
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: b83cba7b-4627-4ba7-b5d0-f1039fe30e78
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: marsma
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: e8fbc4ecdffe62806019f494e1412cfedbccf71f
ms.contentlocale: it-it
ms.lasthandoff: 08/21/2017

---
# <a name="monitor-a-storage-account-in-the-azure-portal"></a>Monitorare un account di archiviazione nel portale di Azure

[Analisi archiviazione di Azure](../storage-analytics.md) offre metriche per tutti i servizi di archiviazione e log per BLOB, code e tabelle. È possibile usare il [portale di Azure](https://portal.azure.com) per configurare quali metriche e log verranno registrati per l'account e i grafici che offriranno una rappresentazione visiva dei dati di metrica.

> [!NOTE]
> All'esame dei dati di monitoraggio nel portale di Azure sono associati costi. Per altre informazioni, vedere [Analisi archiviazione e fatturazione](/rest/api/storageservices/Storage-Analytics-and-Billing).
>
> L’archiviazione file di Azure attualmente supporta la metrica di analisi di archiviazione, ma non supporta ancora l'accesso.
>
> Le funzionalità di metrica e registrazione non sono attualmente abilitate per gli account di archiviazione con replica di tipo archiviazione con ridondanza della zona (ZRS).
> 
> Per una guida dettagliata sull'utilizzo di Analisi archiviazione e di altri strumenti per identificare, diagnosticare e risolvere i problemi relativi ad Archiviazione di Azure, vedere [Monitoraggio, diagnosi e risoluzione dei problemi del servizio di archiviazione di Microsoft Azure](../storage-monitoring-diagnosing-troubleshooting.md).
>

## <a name="configure-monitoring-for-a-storage-account"></a>Configurare il monitoraggio per un account di archiviazione

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Account di archiviazione**e quindi il nome dell'account di archiviazione per aprire il dashboard dell'account.
1. Selezionare **Diagnostica** nella sezione **Monitoraggio** del pannello del menu.

    ![MonitoringOptions](./media/storage-monitor-storage-account/stg-enable-metrics-00.png)

1. Selezionare il **tipo** di dati di metrica per ogni **servizio** che si vuole monitorare e i **criteri di conservazione** per i dati. È anche possibile disabilitare il monitoraggio impostando **Stato** su **Disattivato**.

    ![MonitoringOptions](./media/storage-monitor-storage-account/stg-enable-metrics-01.png)

   Per ogni servizio è possibile abilitare due tipi di metriche, che per i nuovi account di archiviazione sono entrambi abilitati per impostazione predefinita.

   * **Metriche aggregate**: vengono raccolte metriche come ingresso/uscita, disponibilità, latenza e percentuale di operazioni riuscite, che vengono aggregate per i servizi BLOB, di accodamento, tabelle e file.
   * **Per API**: oltre alle metriche aggregate, lo stesso set di metriche viene raccolto per ogni operazione di archiviazione nell'API del servizio Archiviazione di Azure.

   Per impostare i criteri di conservazione dei dati, spostare il dispositivo di scorrimento **Conservazione (in giorni)** oppure immettere il numero desiderato di giorni, da 1 a 365. L'impostazione predefinita per i nuovi account di archiviazione è sette giorni. Se non si desidera impostare criteri di conservazione, immettere zero. Se non vengono impostati criteri di conservazione, i dati di monitoraggio dovranno essere eliminati manualmente.

   > [!WARNING]
   > Quando si eliminano manualmente i dati di metrica, viene applicato un addebito. I dati di analisi obsoleti, che hanno superato l'intervallo di tempo dei criteri di conservazione, vengono eliminati dal sistema gratuitamente. È consigliabile impostare i criteri di conservazione in base al periodo di tempo per il quale si vogliono conservare i dati di analisi dell'archiviazione per l'account. Per altre informazioni, vedere [Quali addebiti è necessario sostenere quando si abilitano le metriche di archiviazione?](../common/storage-enable-and-view-metrics.md#what-charges-do-you-incur-when-you-enable-storage-metrics)
   >

1. Al termine della configurazione del monitoraggio, selezionare **Salva**.

Un set predefinito di metriche verrà visualizzato in grafici nel pannello dell'account di archiviazione e nei pannelli dei singoli servizi (BLOB, accodamento, tabelle e file). Dopo aver abilitato le metriche per un servizio, potrebbe trascorrere fino a un'ora prima che i dati vengano visualizzati nei grafici. È possibile selezionare **Modifica** in qualsiasi grafico delle metriche per [configurare le metriche](#how-to-customize-metrics-charts) visualizzate nel grafico.

È possibile disabilitare la raccolta e la registrazione delle metriche impostando **Stato** su **Disattivato**.

> [!NOTE]
> Archiviazione di Azure usa l'[archivio tabelle](../common/storage-introduction.md#table-storage) per archiviare le metriche per l'account di archiviazione e archivia le metriche in tabelle dell'account. Per altre informazioni, vedere [Come vengono archiviate le metriche](../common/storage-analytics.md#how-metrics-are-stored).
>

## <a name="customize-metrics-charts"></a>Personalizzare i grafici delle metriche

Per scegliere le metriche di archiviazione da visualizzare in un grafico, seguire questa procedura. 

1. Per iniziare, visualizzare un grafico delle metriche di archiviazione nel portale di Azure. I grafici sono riportati nel **pannello dell'account di archiviazione** e nel pannello **Metriche** di un singolo servizio (BLOB, accodamento, tabelle o file).

   In questo esempio verrà usato il grafico seguente, visualizzato nel **pannello dell'account di archiviazione**:

   ![Selezione del grafico nel portale di Azure](./media/storage-monitor-storage-account/stg-customize-chart-00.png)

1. Fare quindi clic in qualsiasi punto del grafico per aprire il pannello **Metrica**. Selezionare **Modifica grafico** per aprire il pannello **Modifica grafico**.

   ![Pulsante Modifica grafico nel pannello del grafico](./media/storage-monitor-storage-account/stg-customize-chart-01.png)

1. Nel pannello **Modifica grafico** selezionare l'**intervallo di tempo** delle metriche da visualizzare nel grafico e il **servizio** (BLOB, accodamento, tabelle o file) di cui si vogliono visualizzare le metriche. In questo esempio si è scelto di visualizzare le metriche della settimana precedente per il servizio BLOB:

   ![Selezione dell'intervallo di tempo e del servizio nel pannello Modifica grafico](./media/storage-monitor-storage-account/stg-customize-chart-02.png)

1. Selezionare le singole **metriche** da visualizzare nel grafico e quindi fare clic su **OK**. In questo esempio si è scelto di visualizzare le metriche *ContainerCount* e *ObjectCount*:

   ![Selezione delle singole metriche nel pannello Modifica grafico](./media/storage-monitor-storage-account/stg-customize-chart-03.png)

Le impostazioni del grafico non influiscono sulla raccolta, l'aggregazione o l'archiviazione dei dati di monitoraggio nell'account di archiviazione, ma solo sulla visualizzazione dei dati di metrica.

### <a name="metrics-availability-in-charts"></a>Metriche disponibili nei grafici

L'elenco delle metriche disponibili varia in base al servizio selezionato nell'elenco a discesa e al tipo di unità del grafico che viene modificato. Ad esempio, è possibile selezionare metriche relative a percentuali come *PercentNetworkError* e *PercentThrottlingError* solo se si sta modificando un grafico che visualizza unità in percentuale:

![Grafico della percentuale di errori nelle richieste nel portale di Azure](./media/storage-monitor-storage-account/stg-customize-chart-04.png)

### <a name="metrics-resolution"></a>Risoluzione delle metriche

Le metriche selezionate in Diagnostica determinano la risoluzione delle metriche disponibili per l'account:

* Il monitoraggio **aggregato** offre metriche come ingresso/uscita, disponibilità, latenza e percentuale di operazioni riuscite, che vengono aggregate dai servizi BLOB, tabelle, file e di accodamento.
* **Per API** offre una risoluzione più dettagliata, con disponibilità di metriche per le singole operazioni di archiviazione in aggiunta alle aggregazioni a livello di servizio.

## <a name="configure-metrics-alerts"></a>Configurare avvisi relativi alle metriche

È possibile creare avvisi per ricevere una notifica quando sono state raggiunte le soglie per le metriche delle risorse di archiviazione.

1. Per aprire il pannello **Regole di avviso**, scorrere verso il basso fino alla sezione **Monitoraggio** del **pannello del menu** e selezionare **Regole di avviso**.
1. Selezionare **Aggiungi avviso** per aprire il pannello **Aggiungi una regola di avviso**.
1. Selezionare una **risorsa** (BLOB, file, coda o tabella) nell'elenco a discesa e immettere un **nome** e una **descrizione** per la nuova regola di avviso.
1. Selezionare la **metrica** per cui si vuole aggiungere un avviso, una **condizione** di avviso e una **soglia**. Il tipo di unità della soglia varia a seconda della metrica scelta. Ad esempio, il tipo di unità per *ContainerCount* è "conteggio", mentre l'unità per la metrica *PercentNetworkError* è una percentuale.
1. Selezionare il **periodo**. Le metriche che raggiungono o superano la soglia nel periodo specificato attivano un avviso.
1. (Facoltativo) Configurare le notifiche per **posta elettronica** e **webhook**. Per altre informazioni sui webhook, vedere [Configurare un webhook in un avviso relativo alle metriche di Azure](../../monitoring-and-diagnostics/insights-webhooks-alerts.md). Se non si configurano notifiche per posta elettronica o webhook, gli avvisi verranno visualizzati solo nel portale di Azure.

![Pannello Aggiungi una regola di avviso nel portale di Azure](./media/storage-monitor-storage-account/stg-alert-rules-01.png)

## <a name="add-metrics-charts-to-the-portal-dashboard"></a>Aggiungere i grafici delle metriche al dashboard del portale

È possibile aggiungere i grafici delle metriche di Archiviazione di Azure per qualsiasi account di archiviazione al dashboard del portale.

1. Fare clic su **Modifica dashboard** mentre si visualizza il dashboard nel [portale di Azure](https://portal.azure.com).
1. In **Raccolta riquadri** selezionare **Trova riquadri per** > **Tipo**.
1. Selezionare **Tipo** > **Account di archiviazione**.
1. In **Risorse** selezionare l'account di archiviazione di cui si vogliono aggiungere le metriche al dashboard.
1. Selezionare **Categorie** > **Monitoraggio**.
1. Trascinare il riquadro del grafico per la metrica da visualizzare sul dashboard. Ripetere l'operazione per tutte le metriche che si vogliono visualizzare nel dashboard. Nell'immagine seguente è evidenziato il grafico "BLOB - Richieste totali" come esempio, ma tutti i grafici sono posizionabili nel dashboard.

   ![Raccolta riquadri nel portale di Azure](./media/storage-monitor-storage-account/stg-customize-dashboard-01.png)
1. Dopo aver completato l'aggiunta dei grafici, selezionare **Fine personalizzazione** nella parte superiore del dashboard.

Dopo che sono stati aggiunti al dashboard, i grafici possono essere ulteriormente personalizzati come descritto in [Personalizzare i grafici delle metriche](#how-to-customize-metrics-charts).

## <a name="configure-logging"></a>Configurare la registrazione

È possibile impostare Archiviazione di Azure in modo da salvare i log di diagnostica per le richieste di lettura, scrittura ed eliminazione per i servizi BLOB, tabelle e di accodamento. I criteri di conservazione dati impostati si applicano anche a questi log.

> [!NOTE]
> L’archiviazione file di Azure attualmente supporta la metrica di analisi di archiviazione, ma non supporta ancora l'accesso.
>

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Account di archiviazione** e quindi il nome dell'account di archiviazione per aprire il relativo pannello.
1. Selezionare **Diagnostica** nella sezione **Monitoraggio** del pannello del menu.

    ![Voce di menu Diagnostica in Monitoraggio nel portale di Azure.](./media/storage-monitor-storage-account/stg-enable-metrics-00.png)
    
1. Verificare che l'opzione **Stato** sia impostata su **Attivato** e selezionare i **servizi** per cui si vuole abilitare la registrazione.

    ![Configurare la registrazione nel portale di Azure.](./media/storage-monitor-storage-account/stg-enable-logging-01.png)
1. Fare clic su **Save**.

I log di diagnostica vengono salvati in un contenitore BLOB denominato $logs nell'account di archiviazione. È possibile visualizzare i dati dei log con uno strumento di esplorazione di archiviazione come [Microsoft Azure Storage Explorer](http://storageexplorer.com) oppure a livello di codice usando la libreria client di archiviazione o PowerShell.

Per informazioni sull'accesso al contenitore $logs, vedere [Enabling Storage Logging and Accessing Log Data](/rest/api/storageservices/enabling-storage-logging-and-accessing-log-data) (Abilitazione della registrazione di archiviazione e accesso ai dati dei log).

## <a name="next-steps"></a>Passaggi successivi

* Altri dettagli su [metriche, registrazione e fatturazione](../storage-analytics.md) per Analisi archiviazione.
* [Abilitazione delle metriche di Archiviazione di Azure e visualizzazione dei dati delle metriche](../storage-enable-and-view-metrics.md) con PowerShell e a livello di codice con C#.


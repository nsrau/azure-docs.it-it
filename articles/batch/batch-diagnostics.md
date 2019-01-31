---
title: Metriche, avvisi e log di diagnostica - Azure Batch | Microsoft Docs
description: Registrare e analizzare gli eventi di registrazione diagnostica per le risorse dell'account Azure Batch, come pool e attività.
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 12/05/2018
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 02129fbddae2e3a82299eb3b03d6e73030b44c4d
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2019
ms.locfileid: "55477880"
---
# <a name="batch-metrics-alerts-and-logs-for-diagnostic-evaluation-and-monitoring"></a>Metriche, avvisi e log di Batch per la valutazione diagnostica e il monitoraggio

 
Questo articolo descrive come monitorare un account Batch tramite le funzionalità di [Monitoraggio di Azure](../azure-monitor/overview.md). Monitoraggio di Azure raccoglie [metriche](../azure-monitor/platform/data-collection.md#metrics) e [log di diagnostica](../azure-monitor/platform/diagnostic-logs-overview.md) per le risorse nell'account Batch. È possibile raccogliere e utilizzare i dati in svariati modi per monitorare l'account Batch e diagnosticare i problemi. È anche possibile configurare [avvisi sulle metriche](../azure-monitor/platform/alerts-overview.md) per ricevere notifiche quando una metrica raggiunge un valore specificato. 

## <a name="batch-metrics"></a>Metriche di Batch

Le metriche sono dati di telemetria di Azure (chiamati anche contatori delle prestazioni) generati dalle risorse di Azure che vengono usate dal servizio Monitoraggio di Azure. Metriche di esempio di un account Batch sono quelle relative a eventi di creazione di pool, numero di nodi con priorità bassa ed eventi di completamento di attività. 

Vedere l'[elenco delle metriche di Batch supportate](../azure-monitor/platform/metrics-supported.md#microsoftbatchbatchaccounts).

Le metriche:

* Vengono abilitate per impostazione predefinita in ogni account Batch senza alcuna configurazione aggiuntiva
* Vengono generate ogni minuto
* Non sono automaticamente persistenti, ma hanno una cronologia in sequenza di 30 giorni. È possibile mantenere persistenti le metriche di attività come parte della [registrazione diagnostica](#work-with-diagnostic-logs).

### <a name="view-metrics"></a>Visualizzare le metriche

È possibile visualizzare le metriche per l'account Batch nel portale di Azure. La pagina **Panoramica** per l'account mostra per impostazione predefinita le metriche relative a nodi, core e attività principali. 

Per visualizzare tutte le metriche dell'account Batch: 

1. Nel portale fare clic su **Tutti i servizi** > **Account Batch** e quindi fare clic sul nome dell'account Batch.
2. In **Monitoraggio** selezionare **Metrica**.
3. Selezionare una o più metriche. Se lo si desidera, è possibile selezionare metriche di risorse aggiuntive tramite gli elenchi a discesa **Sottoscrizioni**, **Gruppo di risorse**, **Tipo di risorsa** e **Risorsa**.

    ![Metriche di Batch](media/batch-diagnostics/metrics-portal.png)

Per recuperare le metriche a livello di codice, usare le API di Monitoraggio di Azure. Ad esempio, vedere [Retrieve Azure Monitor metrics with .NET](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) (Recuperare metriche di Monitoraggio di Azure con .NET).

## <a name="batch-metric-reliability"></a>Affidabilità delle metriche di Batch

Le metriche sono destinate all'uso per l'analisi dei dati e delle tendenze. La trasmissione delle metriche non è garantita ed è soggetta a recapito non ordinato, perdita di dati e/o duplicazione. Non è consigliabile usare singoli eventi come avviso o per attivare funzioni. Per altre informazioni su come impostare le soglie di avviso, vedere la sezione [Avvisi sulle metriche di Batch](#batch-metric-alerts).

Le metriche generate negli ultimi 3 minuti possono essere ancora in fase di aggregazione. Durante questo intervallo di tempo possono essere indicati valori delle metriche inferiori a quelli effettivi.

## <a name="batch-metric-alerts"></a>Avvisi sulle metriche di Batch

Facoltativamente, è possibile configurare *avvisi sulle metriche* praticamente in tempo reale, che vengono attivati quando il valore di una metrica specificata supera la soglia assegnata. L'avviso genera una [notifica](../monitoring-and-diagnostics/insights-alerts-portal.md) personalizzata quando viene "attivato" (quando la soglia viene superata e viene soddisfatta la condizione di avviso) e anche quando viene "risolto" (quando il valore rientra nella soglia e la condizione non viene più soddisfatta). Non è consigliabile generare avvisi in base a singoli punti dati poiché le metriche sono soggette a recapito non ordinato, perdita di dati e/o duplicazione. Per tenere conto di queste incongruenze, gli avvisi devono essere basati su soglie.

Ad esempio, è possibile configurare un avviso sulle metriche quando il numero di core per priorità bassa diminuisce a un determinato livello, per consentire la regolazione della composizione dei pool. È consigliabile impostare un periodo di 10 minuti o più in cui gli avvisi si attivano se il numero medio di core con priorità bassa scende sotto il valore di soglia per l'intero periodo. Non è consigliabile generare avvisi per un intervallo di tempo di 1-5 minuti perché le metriche potrebbero essere ancora in fase di aggregazione.

Per configurare un avviso sulle metriche nel portale:

1. Fare clic su **Tutti i servizi** > **Account Batch** e quindi fare clic sul nome dell'account Batch.
2. In **Monitoraggio** fare clic su **Regole di avviso** > **Aggiungi avviso per la metrica**.
3. Selezionare una metrica, una condizione di avviso, ad esempio quando una metrica supera un valore specifico durante un periodo, e una o più notifiche.

È anche possibile configurare un avviso praticamente in tempo reale usando l'[API REST](https://docs.microsoft.com/rest/api/monitor/). Per altre informazioni, vedere la [panoramica degli avvisi](../azure-monitor/platform/alerts-overview.md)

## <a name="batch-diagnostics"></a>Diagnostica di Batch

I log di diagnostica contengono informazioni generate dalle risorse di Azure che descrivono il funzionamento di ogni risorsa. Per Batch, è possibile raccogliere i log seguenti:

* Eventi **Log del servizio** generati dal servizio Batch di Azure durante il ciclo di vita di una singola risorsa di Batch, come un pool o un'attività. 

* Log **Metrica** a livello di account. 

Le impostazioni per abilitare la raccolta di log di diagnostica non sono attive per impostazione predefinita. È necessario abilitare in modo esplicito le impostazioni di diagnostica per ogni account Batch che si vuole monitorare.

### <a name="log-destinations"></a>Destinazioni dei log

Uno scenario comune consiste nel selezionare un account di archiviazione di Azure come destinazione dei log. Per archiviare i log in Archiviazione di Azure, creare l'account prima di abilitare la raccolta dei log. Se all'account Batch è stato associato un account di archiviazione, è possibile scegliere l'account come destinazione dei log. 

Altre destinazioni facoltative per i log di diagnostica:

* Trasmettere gli eventi dei log di diagnostica di Batch a un [hub eventi di Azure](../event-hubs/event-hubs-what-is-event-hubs.md). Hub eventi è in grado di inserire milioni di eventi al secondo, che è quindi possibile trasformare e archiviare tramite un qualsiasi provider di analisi in tempo reale. 

* Inviare i log di diagnostica ad [Azure Log Analytics](../log-analytics/log-analytics-overview.md), in cui è possibile analizzarli o esportarli per l'analisi in Power BI o Excel.

> [!NOTE]
> Potrebbero essere previsti costi aggiuntivi per archiviare o elaborare dati dei log di diagnostica con servizi di Azure. 
>

### <a name="enable-collection-of-batch-diagnostic-logs"></a>Abilitare la raccolta dei log di diagnostica di Batch

1. Nel portale fare clic su **Tutti i servizi** > **Account Batch** e quindi fare clic sul nome dell'account Batch.
2. In **Monitoraggio** fare clic su **Log di diagnostica** > **Abilita diagnostica**.
3. In **Impostazioni di diagnostica** immettere un nome per l'impostazione e scegliere una destinazione dei log (account di archiviazione esistente, hub eventi o Log Analytics). Selezionare **ServiceLog**, **AllMetrics** o entrambi.

    Quando si seleziona un account di archiviazione, è facoltativamente possibile impostare criteri di conservazione. Se non si specifica un numero di giorni per la conservazione, i dati vengono mantenuti per tutto il ciclo di vita dell'account di archiviazione.

4. Fare clic su **Save**.

    ![Diagnostica di Batch](media/batch-diagnostics/diagnostics-portal.png)

Altre opzioni per abilitare la raccolta di log includono l'uso di Monitoraggio di Azure nel portale per configurare le impostazioni di diagnostica, di un [modello di Resource Manager](../azure-monitor/platform/diagnostic-logs-stream-template.md) oppure di Azure PowerShell o dell'interfaccia della riga di comando di Azure. Vedere [Raccogliere e utilizzare dati dei log dalle risorse di Azure](../azure-monitor/platform/diagnostic-logs-overview.md#how-to-enable-collection-of-diagnostic-logs)


### <a name="access-diagnostics-logs-in-storage"></a>Accedere ai log di diagnostica nell'archiviazione

Se si archiviano i log di diagnostica di Batch in un account di archiviazione, viene creato un contenitore di archiviazione nell'account di archiviazione non appena si verifica un evento correlato. I BLOB vengono creati in base al modello di denominazione seguente:

```
insights-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/
RESOURCEGROUPS/{resource group name}/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/{batch account name}/y={four-digit numeric year}/
m={two-digit numeric month}/d={two-digit numeric day}/
h={two-digit 24-hour clock hour}/m=00/PT1H.json
```
Esempio:

```
insights-metrics-pt1m/resourceId=/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/
RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/MYBATCHACCOUNT/y=2018/m=03/d=05/h=22/m=00/PT1H.json
```
Ogni file di BLOB PT1H.json contiene eventi in formato JSON che si sono verificati nell'ora specificata nell'URL del BLOB, ad esempio, h=12. Durante l'ora attuale, gli eventi vengono aggiunti al file PT1H.json man mano che si verificano. Il valore dei minuti (m=00) è sempre 00, perché gli eventi del log di diagnostica vengono sempre suddivisi in singoli BLOB per ogni ora. Tutte le ore sono in formato UTC.


Per altre informazioni sullo schema dei log di diagnostica nell'account di archiviazione, vedere [Archiviare log di diagnostica di Azure](../azure-monitor/platform/archive-diagnostic-logs.md#schema-of-diagnostic-logs-in-the-storage-account).

Per accedere ai log nell'account di archiviazione a livello di codice, usare le API di Archiviazione. 

### <a name="service-log-events"></a>Eventi del log del servizio
I log del servizio Azure Batch, se raccolti, contengono gli eventi generati dal servizio Azure Batch durante il ciclo di vita di una singola risorsa di Batch, come un pool o un'attività. Ogni evento generato da Batch viene registrato in formato JSON. Ad esempio, questo è il corpo di un **evento di creazione pool** di esempio:

```json
{
    "poolId": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Small",
    "cloudServiceConfiguration": {
        "osFamily": "5",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "resizeTimeout": "300000",
    "targetDedicatedComputeNodes": 2,
    "maxTasksPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoscale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

Il servizio Batch attualmente emette gli eventi di log del servizio seguenti. Questo elenco potrebbe non essere completo, poiché è possibile che eventi aggiuntivi siano stati aggiunti dopo l'ultimo aggiornamento di questo articolo.

| **Eventi del log del servizio** |
| --- |
| [Creazione di pool](batch-pool-create-event.md) |
| [Avvio dell'eliminazione di pool](batch-pool-delete-start-event.md) |
| [Completamento dell'eliminazione di pool](batch-pool-delete-complete-event.md) |
| [Avvio del ridimensionamento di pool](batch-pool-resize-start-event.md) |
| [Completamento del ridimensionamento di pool](batch-pool-resize-complete-event.md) |
| [Avvio dell'attività](batch-task-start-event.md) |
| [Attività completata](batch-task-complete-event.md) |
| [Errore dell'attività](batch-task-fail-event.md) |



## <a name="next-steps"></a>Passaggi successivi

* Informazioni sulle [API e gli strumenti di Batch](batch-apis-tools.md) disponibili per la compilazione di soluzioni Batch.
* Leggere altre informazioni sul [monitoraggio di soluzioni Batch](monitoring-overview.md).

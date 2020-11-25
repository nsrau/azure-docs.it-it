---
title: Metriche, avvisi e log di diagnostica
description: Registrare e analizzare gli eventi di registrazione diagnostica per le risorse dell'account Azure Batch, come pool e attività.
ms.topic: how-to
ms.date: 10/08/2020
ms.custom: seodec18
ms.openlocfilehash: fe2697c73f2a5f3f0b33cfb598f11f39420ed723
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95994112"
---
# <a name="batch-metrics-alerts-and-logs-for-diagnostic-evaluation-and-monitoring"></a>Metriche, avvisi e log di Batch per la valutazione diagnostica e il monitoraggio

Questo articolo descrive come monitorare un account Batch tramite le funzionalità di [Monitoraggio di Azure](../azure-monitor/overview.md). Monitoraggio di Azure raccoglie [metriche](../azure-monitor/platform/data-platform-metrics.md) e [log di diagnostica](../azure-monitor/platform/platform-logs-overview.md) per le risorse nell'account Batch. È possibile raccogliere e utilizzare i dati in svariati modi per monitorare l'account Batch e diagnosticare i problemi. È anche possibile configurare [avvisi sulle metriche](../azure-monitor/platform/alerts-overview.md) per ricevere notifiche quando una metrica raggiunge un valore specificato.

## <a name="batch-metrics"></a>Metriche di Batch

Le metriche sono dati di telemetria di Azure (detti anche contatori delle prestazioni) che vengono emessi dalle risorse di Azure e utilizzati dal servizio monitoraggio di Azure. Esempi di metriche in un account batch sono gli eventi di creazione del pool, il numero di nodi Low-Priority e gli eventi di completamento dell'attività.

Vedere l'[elenco delle metriche di Batch supportate](../azure-monitor/platform/metrics-supported.md#microsoftbatchbatchaccounts).

Le metriche:

- Vengono abilitate per impostazione predefinita in ogni account Batch senza alcuna configurazione aggiuntiva
- Vengono generate ogni minuto
- Non sono automaticamente persistenti, ma hanno una cronologia in sequenza di 30 giorni. È possibile mantenere persistenti le metriche di attività come parte della registrazione diagnostica.

## <a name="view-batch-metrics"></a>Visualizzare le metriche batch

Nella portale di Azure, nella pagina **Panoramica** dell'account verranno visualizzate le metriche principali per nodo, core e attività, per impostazione predefinita.

Per visualizzare tutte le metriche dell'account batch nel portale di Azure:

1. Nella portale di Azure selezionare tutti i **Servizi**  >  **account batch** e quindi selezionare il nome dell'account batch.
2. In **Monitoraggio** selezionare **Metrica**.
3. Selezionare **Aggiungi metrica** , quindi scegliere una metrica dall'elenco a discesa.
4. Selezionare un'opzione di **aggregazione** per la metrica. Per le metriche basate su conteggi (ad esempio "conteggio dei core dedicati" o "numero di nodi con priorità bassa"), usare l'aggregazione **media** . Per le metriche basate su eventi, ad esempio "eventi di completamento ridimensionamento pool", usare l'aggregazione **count**.

   > [!WARNING]
   > Non utilizzare l'aggregazione "sum", che somma i valori di tutti i punti dati ricevuti nel periodo del grafico.

5. Per aggiungere altre metriche, ripetere i passaggi 3 e 4.

È anche possibile recuperare le metriche a livello di codice con le API di monitoraggio di Azure. Per un esempio, vedere [recuperare le metriche di monitoraggio di Azure con .NET](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/).

### <a name="batch-metric-reliability"></a>Affidabilità delle metriche di Batch

Le metriche consentono di identificare le tendenze e possono essere usate per l'analisi dei dati. È importante notare che il recapito della metrica non è garantito e può essere soggetto a recapito non ordinato, perdita di dati e/o duplicazione. Per questo motivo, non è consigliabile usare eventi singoli per avvisi o trigger di funzioni. Per ulteriori informazioni su come impostare le soglie per gli avvisi, vedere la sezione successiva.

Le metriche emesse negli ultimi 3 minuti potrebbero ancora essere aggregate, quindi i valori delle metriche possono essere sottosegnalati durante questo intervallo di tempo.

## <a name="batch-metric-alerts"></a>Avvisi sulle metriche di Batch

È possibile configurare gli *avvisi delle metriche* near Real Time che si attivano quando il valore di una metrica specificata supera una soglia assegnata. L'avviso genera una notifica quando viene "attivato" (quando la soglia viene superata e viene soddisfatta la condizione di avviso) e quando viene "risolto" (quando il valore rientra nella soglia e la condizione non viene più soddisfatta).

Non è consigliabile eseguire avvisi che vengono attivati in un singolo punto dati, in quanto le metriche sono soggette al recapito non ordinato, alla perdita di dati e/o alla duplicazione. Quando si creano gli avvisi, è possibile utilizzare le soglie per tenere conto di tali incoerenze.

Ad esempio, è possibile configurare un avviso sulle metriche quando il numero di core per priorità bassa diminuisce a un determinato livello, per consentire la regolazione della composizione dei pool. Per ottenere risultati ottimali, impostare un periodo di 10 o più minuti, in cui gli avvisi vengono attivati se il numero medio di core a bassa priorità scende al di sotto del valore di soglia per l'intero periodo. Questo consente più tempo per l'aggregazione delle metriche, in modo da ottenere risultati più accurati.

Per configurare un avviso per la metrica nel portale di Azure:

1. Selezionare **Tutti i servizi** > **Account Batch** e quindi selezionare il nome dell'account Batch.
2. In **monitoraggio** selezionare **avvisi**, quindi selezionare **nuova regola di avviso**.
3. Fare clic su **Seleziona condizione**, quindi scegliere una metrica. Confermare i valori per **periodo del grafico**, **tipo di soglia**, **operatore** e tipo di **aggregazione** e immettere un **valore soglia**. Al termine selezionare **Done** (Fine).
4. Aggiungere un gruppo di azioni per l'avviso selezionando un gruppo di azioni esistente o creandone uno nuovo.
5. Nella sezione **Dettagli regola di avviso** immettere un nome e una **Descrizione** della **regola di avviso** e selezionare il livello di **gravità**
6. Selezionare **Crea regola di avviso**.

Per altre informazioni sulla creazione di avvisi per le metriche, vedere [comprendere il funzionamento degli avvisi delle metriche in monitoraggio di Azure](../azure-monitor/platform/alerts-metric-overview.md) e [creare, visualizzare e gestire gli avvisi delle metriche con monitoraggio di Azure](../azure-monitor/platform/alerts-metric.md).

È anche possibile configurare un avviso quasi in tempo reale usando l' [API REST](/rest/api/monitor/)di monitoraggio di Azure. Per ulteriori informazioni, vedere [Panoramica degli avvisi in Microsoft Azure](../azure-monitor/platform/alerts-overview.md). Per includere le informazioni specifiche relative a processi, attività o pool negli avvisi, vedere le informazioni sulle query di ricerca in [rispondere agli eventi con gli avvisi di monitoraggio di Azure](../azure-monitor/learn/tutorial-response.md).

## <a name="batch-diagnostics"></a>Diagnostica di Batch

I log di diagnostica contengono informazioni generate dalle risorse di Azure che descrivono il funzionamento di ogni risorsa. Per Batch, è possibile raccogliere i log seguenti:

- Eventi **Log del servizio** generati dal servizio Batch di Azure durante il ciclo di vita di una singola risorsa di Batch, come un pool o un'attività.
- Log **Metrica** a livello di account.

Le impostazioni per abilitare la raccolta di log di diagnostica non sono attive per impostazione predefinita. È necessario abilitare in modo esplicito le impostazioni di diagnostica per ogni account Batch che si vuole monitorare.

### <a name="log-destinations"></a>Destinazioni dei log

Uno scenario comune consiste nel selezionare un account di archiviazione di Azure come destinazione dei log. Per archiviare i log in Archiviazione di Azure, creare l'account prima di abilitare la raccolta dei log. Se all'account Batch è stato associato un account di archiviazione, è possibile scegliere l'account come destinazione dei log.

In alternativa, è possibile:

- Trasmettere gli eventi dei log di diagnostica di Batch a un [hub eventi di Azure](../event-hubs/event-hubs-about.md). Hub eventi è in grado di inserire milioni di eventi al secondo, che è quindi possibile trasformare e archiviare tramite un qualsiasi provider di analisi in tempo reale.
- Inviare i log di diagnostica ai [log di Monitoraggio di Azure](../azure-monitor/log-query/log-query-overview.md) dove è possibile analizzarli o esportarli per l'analisi in Power BI o Excel.

> [!NOTE]
> Potrebbero essere previsti costi aggiuntivi per archiviare o elaborare dati dei log di diagnostica con servizi di Azure.

### <a name="enable-collection-of-batch-diagnostic-logs"></a>Abilitare la raccolta dei log di diagnostica di Batch

Per creare una nuova impostazione di diagnostica nella portale di Azure, attenersi alla procedura riportata di seguito.

1. Nella portale di Azure selezionare tutti i **Servizi**  >  **account batch** e quindi selezionare il nome dell'account batch.
2. Selezionare **Impostazioni di diagnostica** in **Monitoraggio**.
3. In **impostazioni di diagnostica** selezionare **Aggiungi impostazione di diagnostica**.
4. Immettere un nome per l'impostazione.
5. Selezionare una destinazione: **inviare a log Analytics**, **archiviare in un account di archiviazione** o **trasmettere a un hub eventi**. Se si seleziona un account di archiviazione, è possibile impostare facoltativamente un criterio di conservazione. Se non si specifica un numero di giorni per la conservazione, i dati vengono mantenuti per tutto il ciclo di vita dell'account di archiviazione.
6. Selezionare **ServiceLog**, **AllMetrics** o entrambi.
7. Selezionare **Save (Salva** ) per creare l'impostazione di diagnostica.

È anche possibile [abilitare la raccolta tramite monitoraggio di Azure nel portale di Azure](../azure-monitor/platform/diagnostic-settings.md) per configurare le impostazioni di diagnostica, usando un [modello di Gestione risorse](../azure-monitor/samples/resource-manager-diagnostic-settings.md)o con Azure PowerShell o l'interfaccia della riga di comando di Azure. Per altre informazioni, vedere [Panoramica dei log della piattaforma Azure](../azure-monitor/platform/platform-logs-overview.md).

### <a name="access-diagnostics-logs-in-storage"></a>Accedere ai log di diagnostica nell'archiviazione

Se si archiviano i log di diagnostica di Batch in un account di archiviazione, viene creato un contenitore di archiviazione nell'account di archiviazione non appena si verifica un evento correlato. I BLOB vengono creati in base al modello di denominazione seguente:

```json
insights-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/
RESOURCEGROUPS/{resource group name}/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/{Batch account name}/y={four-digit numeric year}/
m={two-digit numeric month}/d={two-digit numeric day}/
h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Ad esempio:

```json
insights-metrics-pt1m/resourceId=/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/
RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/MYBATCHACCOUNT/y=2018/m=03/d=05/h=22/m=00/PT1H.json
```

Ogni file BLOB `PT1H.json` contiene eventi in formato JSON che si sono verificati nell'ora specificata nell'URL del BLOB, ad esempio `h=12`. Nell'ora corrente gli eventi che si verificano vengono aggiunti al file `PT1H.json`. Il valore dei minuti (`m=00`) è sempre `00` poiché gli eventi del log di diagnostica vengono suddivisi in singoli BLOB per ogni ora. Tutte le ore sono in formato UTC.

Di seguito è riportato un esempio di voce `PoolResizeCompleteEvent` in un file di log `PT1H.json`. Sono incluse informazioni sul numero corrente e di destinazione dei nodi dedicati e a priorità bassa, oltre all'ora di inizio e di fine dell'operazione:

```json
{ "Tenant": "65298bc2729a4c93b11c00ad7e660501", "time": "2019-08-22T20:59:13.5698778Z", "resourceId": "/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/BATCHACCOUNTS/MYBATCHACCOUNT/", "category": "ServiceLog", "operationName": "PoolResizeCompleteEvent", "operationVersion": "2017-06-01", "properties": {"id":"MYPOOLID","nodeDeallocationOption":"Requeue","currentDedicatedNodes":10,"targetDedicatedNodes":100,"currentLowPriorityNodes":0,"targetLowPriorityNodes":0,"enableAutoScale":false,"isAutoPool":false,"startTime":"2019-08-22 20:50:59.522","endTime":"2019-08-22 20:59:12.489","resultCode":"Success","resultMessage":"The operation succeeded"}}
```

Per altre informazioni sullo schema dei log di diagnostica nell'account di archiviazione, vedere [archiviare i log delle risorse di Azure nell'account di archiviazione](../azure-monitor/platform/resource-logs.md#send-to-azure-storage). Per accedere ai log nell'account di archiviazione a livello di codice, usare le API di Archiviazione.

### <a name="service-log-events"></a>Eventi del log del servizio

I registri dei servizi di Azure Batch, se raccolti, contengono gli eventi generati dal servizio Azure Batch durante la durata di una singola risorsa batch, ad esempio un pool o un'attività. Ogni evento generato da Batch viene registrato in formato JSON. Ad esempio, questo è il corpo di un **evento di creazione pool** di esempio:

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
    "taskSlotsPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoscale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

Gli eventi del log del servizio generati dal servizio batch includono quanto segue:

- [Creazione di pool](batch-pool-create-event.md)
- [Avvio dell'eliminazione di pool](batch-pool-delete-start-event.md)
- [Completamento dell'eliminazione di pool](batch-pool-delete-complete-event.md)
- [Avvio del ridimensionamento di pool](batch-pool-resize-start-event.md)
- [Completamento del ridimensionamento di pool](batch-pool-resize-complete-event.md)
- [Scalabilità automatica del pool](batch-pool-autoscale-event.md)
- [Avvio dell'attività](batch-task-start-event.md)
- [Attività completata](batch-task-complete-event.md)
- [Errore dell'attività](batch-task-fail-event.md)
- [Pianificazione attività non riuscita](batch-task-schedule-fail-event.md)

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulle [API e gli strumenti di Batch](batch-apis-tools.md) disponibili per la compilazione di soluzioni Batch.
- Leggere altre informazioni sul [monitoraggio di soluzioni Batch](monitoring-overview.md).
---
title: Stima dei costi del piano di consumo in Funzioni di AzureStimating Consumption plan costs in Azure Functions
description: Informazioni su come stimare meglio i costi che possono verificarsi durante l'esecuzione dell'app per le funzioni in un piano di consumo in Azure.Learn how to better estimate the costs that you may incur when running your function app in a Consumption plan in Azure.
ms.date: 9/20/2019
ms.topic: conceptual
ms.openlocfilehash: 0e3177d7c65eb1624441427f123e6f95095bdbbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76963989"
---
# <a name="estimating-consumption-plan-costs"></a>Stima dei costi del piano di consumo

Esistono attualmente tre tipi di piani di hosting per un'app che viene eseguita in Funzioni di Azure, con ogni piano con il proprio modello di determinazione dei prezzi:There are currently three types of hosting plans for an app that runs in Azure Functions, with each plan having its own pricing model: 

| Piano | Descrizione |
| ---- | ----------- |
| [**Consumo**](functions-scale.md#consumption-plan) | Ti viene addebitato solo per il momento in cui viene eseguita l'app per le funzioni. Questo piano include una[pagina dei prezzi] di [concessione gratuita]per ogni sottoscrizione.|
| [**Premium**](functions-scale.md#premium-plan) | Fornisce le stesse funzionalità e il meccanismo di ridimensionamento del piano di consumo, ma con prestazioni avanzate e accesso VNET. Il costo si basa sul piano tariffario scelto. Per altre informazioni, vedere Piano Premium di Funzioni di Azure.To learn more, see [Azure Functions Premium plan](functions-premium-plan.md). |
| [**Dedicato (servizio app)**](functions-scale.md#app-service-plan) <br/>(livello base o superiore) | Quando è necessario eseguire in macchine virtuali dedicate o in isolamento, usare immagini personalizzate o usare la capacità del piano di servizio app in eccesso. Utilizza la [fatturazione regolare del piano di servizio app](https://azure.microsoft.com/pricing/details/app-service/). Il costo si basa sul piano tariffario scelto.|

È stato scelto il piano che meglio supporta i requisiti di costo e prestazioni delle funzioni. Per altre informazioni, vedere [Ridimensionamento e hosting di Funzioni di Azure](functions-scale.md).

In questo articolo viene utilizzato solo il piano Consumo, poiché questo piano comporta costi variabili. Questo articolo sostituisce l'articolo Domande frequenti sulla fatturazione dei costi del piano di [consumo.](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ)

Le funzioni durevoli possono essere eseguite anche in un piano di consumo. Per altre informazioni sulle considerazioni relative ai costi quando si usano funzioni durevoli, vedere Fatturazione di [funzioni durevoli](./durable/durable-functions-billing.md).

## <a name="consumption-plan-costs"></a>Costi del piano a consumo

Il *costo* di esecuzione di una singola funzione di esecuzione viene misurato in *GB-secondi*. Il costo di esecuzione viene calcolato combinando l'utilizzo della memoria con il tempo di esecuzione. Una funzione che viene eseguita per costi più lunghi costa di più, così come una funzione che consuma più memoria. 

Si consideri un caso in cui la quantità di memoria utilizzata dalla funzione rimane costante. In questo caso, il calcolo del costo è una semplice moltiplicazione. Si supponga, ad esempio, che la funzione consumata 0,5 GB per 3 secondi. Quindi il costo `0.5GB * 3s = 1.5 GB-seconds`di esecuzione è . 

Poiché l'utilizzo della memoria cambia nel tempo, il calcolo è essenzialmente l'integrale dell'utilizzo della memoria nel tempo.  Il sistema esegue questo calcolo campionando l'utilizzo della memoria del processo (insieme ai processi figlio) a intervalli regolari. Come indicato nella pagina dei [prezzi,]l'utilizzo della memoria viene arrotondato per eccesso al bucket di 128 MB più vicino. Quando il processo utilizza 160 MB, ti vengono addebitati 256 MB. Il calcolo prende in considerazione la concorrenza, ovvero più esecuzioni di funzioni simultanee nello stesso processo.

> [!NOTE]
> Anche se l'utilizzo della CPU non viene considerato direttamente nel costo di esecuzione, può avere un impatto sul costo quando influisce sul tempo di esecuzione della funzione.

## <a name="other-related-costs"></a>Altri costi correlati

Quando si stima il costo complessivo dell'esecuzione delle funzioni in qualsiasi piano, tenere presente che il runtime di Funzioni usa diversi altri servizi di Azure, che vengono fatturati separatamente. Quando si calcolano i prezzi per le app per le funzioni, tutti i trigger e le associazioni disponibili che si integrano con altri servizi di Azure richiedono la creazione e il pagamento di tali servizi aggiuntivi. 

Per le funzioni in esecuzione in un piano di consumo, il costo totale è il costo di esecuzione delle funzioni, oltre al costo della larghezza di banda e dei servizi aggiuntivi. 

Quando si stimano i costi complessivi dell'app per le funzioni e dei servizi correlati, usare il [calcolatore dei prezzi](https://azure.microsoft.com/pricing/calculator/?service=functions)di Azure. 

| Costo correlato | Descrizione |
| ------------ | ----------- |
| **Account di archiviazione** | Per ogni app per le funzioni è necessario disporre di un [account di archiviazione](../storage/common/storage-introduction.md#types-of-storage-accounts)di Azure per scopi generali associato, che viene fatturato [separatamente.](https://azure.microsoft.com/pricing/details/storage/) Questo account viene usato internamente dal runtime di Funzioni, ma è anche possibile usarlo per i trigger e le associazioni di archiviazione. Se non si dispone di un account di archiviazione, ne viene creato uno automaticamente quando viene creata l'app per le funzioni. Per altre informazioni, vedere [Requisiti dell'account di archiviazione](storage-considerations.md#storage-account-requirements).|
| **Application Insights** | Le funzioni si basano su [Application Insights](../azure-monitor/app/app-insights-overview.md) per offrire un'esperienza di monitoraggio ad alte prestazioni per le app per le funzioni. Sebbene non sia obbligatorio, è [consigliabile abilitare l'integrazione](functions-monitoring.md#enable-application-insights-integration)di Application Insights . Ogni mese è inclusa una concessione gratuita di dati di telemetria. Per altre informazioni, vedere [la pagina dei prezzi di Monitoraggio di Azure.To](https://azure.microsoft.com/pricing/details/monitor/)learn more, see the Azure Monitor pricing page . |
| **Larghezza di banda di rete** | Non si paga per il trasferimento dei dati tra i servizi di Azure nella stessa area. Tuttavia, è possibile sostenere i costi per i trasferimenti di dati in uscita in un'altra area o all'esterno di Azure.However, you can incur costs for outbound data transfers to another region or outside of Azure. Per ulteriori informazioni, consulta [Dettagli sui prezzi della larghezza di banda](https://azure.microsoft.com/pricing/details/bandwidth/). |

## <a name="behaviors-affecting-execution-time"></a>Comportamenti che influiscono sul tempo di esecuzioneBehaviors affecting execution time

I seguenti comportamenti delle funzioni possono influire sul tempo di esecuzione:

+ **Trigger e associazioni:** il tempo impiegato per leggere l'input e scrivere l'output nelle associazioni di [funzioni](functions-triggers-bindings.md) viene conteggiato come tempo di esecuzione. Ad esempio, quando la funzione usa un'associazione di output per scrivere un messaggio in una coda di archiviazione di Azure, il tempo di esecuzione include il tempo impiegato per scrivere il messaggio nella coda, incluso nel calcolo del costo della funzione. 

+ **Esecuzione asincrona:** il tempo di attesa della funzione`await` per i risultati di una richiesta asincrona (in C ) viene conteggiato come tempo di esecuzione. Il calcolo GB-secondo si basa sull'ora di inizio e di fine della funzione e sull'utilizzo della memoria in tale periodo. Ciò che sta accadendo in quel tempo in termini di attività della CPU non è fattorizzato nel calcolo. È possibile ridurre i costi durante le operazioni asincrone utilizzando [Funzioni durevoli](durable/durable-functions-overview.md). Non ti viene addebitato il tempo trascorso in attesa nelle funzioni dell'orchestratore.

## <a name="view-execution-data"></a>Visualizzare i dati di esecuzione

Nella [fattura](/azure/billing/billing-download-azure-invoice)è possibile visualizzare i dati relativi ai costi di **Esecuzioni totali - Funzioni** e Tempo di esecuzione - **Funzioni**, insieme ai costi fatturati effettivi. Tuttavia, questi dati della fattura sono un'aggregazione mensile per un periodo di fatturazione passato. 

Per comprendere meglio l'impatto sui costi delle funzioni, è possibile usare Monitoraggio di Azure per visualizzare le metriche correlate ai costi attualmente generate dalle app per le funzioni. È possibile usare [Esplora metriche](../azure-monitor/platform/metrics-getting-started.md) di Monitoraggio di Azure nel portale di [Azure] o API REST per ottenere questi dati.

### <a name="monitor-metrics-explorer"></a>Monitorare Esplora metriche

Usare [Esplora metriche](../azure-monitor/platform/metrics-getting-started.md) di Monitoraggio di Azure per visualizzare i dati relativi ai costi per le app delle funzioni del piano di consumo in un formato grafico. 

1. Nella parte superiore del portale di [Azure] in Ricerca di **servizi, risorse e documenti** cerca `monitor` e seleziona **Monitoraggio** in **Servizi**.

1. A sinistra, seleziona **Metriche** > **Seleziona una risorsa,** quindi usa le impostazioni sotto l'immagine per scegliere l'app per le funzioni.

    ![Selezionare la risorsa dell'app per le funzioni](media/functions-consumption-costing/select-a-resource.png)

      
    |Impostazione  |Valore consigliato  |Descrizione  |
    |---------|---------|---------|
    | Subscription    |  Sottoscrizione in uso  | Sottoscrizione con l'app per le funzioni.  |
    | Resource group     | Gruppo di risorse in uso  | Gruppo di risorse che contiene l'app per le funzioni.   |
    | Tipo di risorsa     |  Servizi app | Le app per le funzioni vengono visualizzate come istanze di Servizi app in Monitor.Function apps are shown as App Services instances in Monitor. |
    | Risorsa     |  La tua app per le funzioni  | App per le funzioni da monitorare.        |

1. Selezionare **Applica** per scegliere l'app per le funzioni come risorsa da monitorare.

1. Da **Metrica**scegliere **Conteggio esecuzione funzione** e **Somma** per **aggregazione.** In questo modo la somma dei conteggi di esecuzione durante il periodo scelto viene aggiunta al grafico.

    ![Definire una metrica dell'app Funzioni da aggiungere al graficoDefine a functions app metric to add to the chart](media/functions-consumption-costing/monitor-metrics-add-metric.png)

1. Selezionare **Aggiungi metrica** e ripetere i passaggi da 2 a 4 per aggiungere unità di **esecuzione** delle funzioni al grafico. 

Il grafico risultante contiene i totali per entrambe le metriche di esecuzione nell'intervallo di tempo scelto, che in questo caso è di due ore.

![Grafico dei conteggi di esecuzione delle funzioni e delle unità di esecuzione](media/functions-consumption-costing/monitor-metrics-execution-sum.png)

Poiché il numero di unità di esecuzione è molto maggiore del numero di esecuzioni, il grafico mostra solo le unità di esecuzione.

Questo grafico mostra un totale di `Function Execution Units` 1,11 miliardi consumati in un periodo di due ore, misurato in MB-millisecondi. Per convertire in GB-secondi, dividere per 1024000. In questo esempio, l'app per le funzioni ha consumato `1110000000 / 1024000 = 1083.98` GB secondi. È possibile prendere questo valore e moltiplicare per il prezzo corrente del tempo di esecuzione nella[pagina dei prezzi]della pagina dei prezzi di [Funzioni], che fornisce il costo di queste due ore, supponendo che siano già state utilizzate le concessioni gratuite di tempo di esecuzione. 

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

[L'interfaccia della riga di comando](/cli/azure/) di Azure include comandi per il recupero delle metriche. È possibile usare l'interfaccia della riga di comando da un ambiente di comando locale o direttamente dal portale tramite [Azure Cloud Shell.](../cloud-shell/overview.md) Ad esempio, il comando [dell'elenco delle metriche di monitoraggio az](/cli/azure/monitor/metrics#az-monitor-metrics-list) seguente restituisce dati orari nello stesso periodo di tempo utilizzato in precedenza.

Assicurarsi di `<AZURE_SUBSCRIPTON_ID>` sostituire con l'ID sottoscrizione di Azure che esegue il comando.

```azurecli-interactive
az monitor metrics list --resource /subscriptions/<AZURE_SUBSCRIPTION_ID>/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption --metric FunctionExecutionUnits,FunctionExecutionCount --aggregation Total --interval PT1H --start-time 2019-09-11T21:46:00Z --end-time 2019-09-11T23:18:00Z
```

Questo comando restituisce un payload JSON simile all'esempio seguente:This command returns a JSON payload that looks like the following example:

```json
{
  "cost": 0.0,
  "interval": "1:00:00",
  "namespace": "Microsoft.Web/sites",
  "resourceregion": "centralus",
  "timespan": "2019-09-11T21:46:00Z/2019-09-11T23:18:00Z",
  "value": [
    {
      "id": "/subscriptions/XXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXX/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption/providers/Microsoft.Insights/metrics/FunctionExecutionUnits",
      "name": {
        "localizedValue": "Function Execution Units",
        "value": "FunctionExecutionUnits"
      },
      "resourceGroup": "metrics-testing-consumption",
      "timeseries": [
        {
          "data": [
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T21:46:00+00:00",
              "total": 793294592.0
            },
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T22:46:00+00:00",
              "total": 316576256.0
            }
          ],
          "metadatavalues": []
        }
      ],
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    },
    {
      "id": "/subscriptions/XXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXX/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption/providers/Microsoft.Insights/metrics/FunctionExecutionCount",
      "name": {
        "localizedValue": "Function Execution Count",
        "value": "FunctionExecutionCount"
      },
      "resourceGroup": "metrics-testing-consumption",
      "timeseries": [
        {
          "data": [
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T21:46:00+00:00",
              "total": 33538.0
            },
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T22:46:00+00:00",
              "total": 13040.0
            }
          ],
          "metadatavalues": []
        }
      ],
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    }
  ]
}
```
Questa particolare risposta `2019-09-11T21:46` mostra `2019-09-11T23:18`che da a , l'applicazione utilizzato 111000000MB MB-millisecondi (1083.98 GB-secondi).

## <a name="determine-memory-usage"></a>Determinare l'utilizzo della memoria

Le unità di esecuzione delle funzioni sono una combinazione di tempo di esecuzione e utilizzo della memoria, il che rende una metrica difficile per comprendere l'utilizzo della memoria. I dati di memoria non sono una metrica attualmente disponibile tramite Monitoraggio di Azure.Memory data isn't a metric currently available through Azure Monitor. Tuttavia, se vuoi ottimizzare l'utilizzo della memoria della tua app, puoi usare i dati dei contatori delle prestazioni raccolti da Application Insights.  

Se non è già stato fatto, [abilitare Application Insights nell'app per](functions-monitoring.md#enable-application-insights-integration)le funzioni. Con questa integrazione abilitata, è possibile eseguire query sui dati di [telemetria nel portale.](functions-monitoring.md#query-telemetry-data)  

In **Monitoraggio**selezionare **Registri (Analisi)**, quindi copiare la query di telemetria seguente e incollarla nella finestra della query e selezionare **Esegui**. Questa query restituisce l'utilizzo totale della memoria a ogni ora campionata.

```
performanceCounters
| where name == "Private Bytes"
| project timestamp, name, value
```

I risultati sono simili all'esempio seguente:The results look like the following example:

| UTC \[timestamp\]          | name          | value       |
|----------------------------|---------------|-------------|
| 12/09/2019, 0:05:14\.947 | Private Bytes | 209,932,288 |
| 12/09/2019, 1:06:14\.994 AM | Private Bytes | 212,189,184 |
| 12/09/2019, 06:30\.010 AM | Private Bytes | 231,714,816 |
| 12/09/2019, 0:07:15\.040 AM | Private Bytes | 210,591,744 |
| 12/09/2019, 1:12:16\.285 AM | Private Bytes | 216,285,184 |
| 12/09/2019, 1:12:31\.376 AM | Private Bytes | 235,806,720 |

## <a name="function-level-metrics"></a>Metriche a livello di funzioneFunction-level metrics

Monitoraggio di Azure tiene traccia delle metriche a livello di risorsa, che per Funzioni è l'app per le funzioni. L'integrazione di Application Insights genera metriche in base alle funzioni. Ecco una query di analisi di esempio per ottenere la durata media di una funzione:Here's an example analytics query to get the average duration of a function:

```
customMetrics
| where name contains "Duration"
| extend averageDuration = valueSum / valueCount
| summarize averageDurationMilliseconds=avg(averageDuration) by name
```

| name                       | averageDurationMilliseconds |
|----------------------------|-----------------------------|
| AvgDurationM QueueTrigger | 16\.087                     |
| QueueTrigger MaxDurationMs | 90\.249                     |
| QueueTrigger MinDurationMs | 8\.522                      |

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Altre informazioni sul monitoraggio delle app per le funzioni](functions-monitoring.md)

[pagina dei prezzi]:https://azure.microsoft.com/pricing/details/functions/
[Portale di Azure]: https://portal.azure.com

---
title: Attività flusso di dati
description: Come eseguire i flussi di dati dall'interno di una pipeline data factory.
services: data-factory
documentationcenter: ''
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: makromer
ms.date: 04/30/2020
ms.openlocfilehash: 5593b0d633b133c8a8295634b674218d5e6c6daf
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2020
ms.locfileid: "89485038"
---
# <a name="data-flow-activity-in-azure-data-factory"></a>Attività flusso di dati in Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Utilizzare l'attività flusso di dati per trasformare e spostare i dati tramite il mapping di flussi di dati. Se non si ha familiarità con i flussi di dati, vedere [Panoramica del flusso di dati di mapping](concepts-data-flow-overview.md)

## <a name="syntax"></a>Sintassi

```json
{
    "name": "MyDataFlowActivity",
    "type": "ExecuteDataFlow",
    "typeProperties": {
      "dataflow": {
         "referenceName": "MyDataFlow",
         "type": "DataFlowReference"
      },
      "compute": {
         "coreCount": 8,
         "computeType": "General"
      },
      "staging": {
          "linkedService": {
              "referenceName": "MyStagingLinkedService",
              "type": "LinkedServiceReference"
          },
          "folderPath": "my-container/my-folder"
      },
      "integrationRuntime": {
          "referenceName": "MyDataFlowIntegrationRuntime",
          "type": "IntegrationRuntimeReference"
      }
}

```

## <a name="type-properties"></a>Proprietà del tipo

Proprietà | Descrizione | Valori consentiti | Obbligatoria
-------- | ----------- | -------------- | --------
Dataflow | Riferimento al flusso di dati in esecuzione | DataFlowReference | Sì
integrationRuntime | Ambiente di calcolo in cui viene eseguito il flusso di dati. Se non è specificato, verrà usato il runtime di integrazione di Azure per la risoluzione automatica. | IntegrationRuntimeReference | No
Compute. coreCount | Il numero di core usati nel cluster Spark. Può essere specificato solo se viene usato il runtime di integrazione di Azure per la risoluzione automatica | 8, 16, 32, 48, 80, 144, 272 | No
Compute. computeType | Tipo di calcolo usato nel cluster Spark. Può essere specificato solo se viene usato il runtime di integrazione di Azure per la risoluzione automatica | "General", "ComputeOptimized", "MemoryOptimized" | No
staging. linkedService | Se si usa un'origine o un sink di analisi sinapsi di Azure, l'account di archiviazione usato per la gestione temporanea di base | LinkedServiceReference | Solo se il flusso di dati legge o scrive in un'analisi di sinapsi di Azure
staging. folderPath | Se si usa un'origine o un sink di analisi sinapsi di Azure, il percorso della cartella nell'account di archiviazione BLOB usato per la gestione temporanea di base | string | Solo se il flusso di dati legge o scrive in Azure sinapsi Analytics

![Esegui flusso di dati](media/data-flow/activity-data-flow.png "Esegui flusso di dati")

### <a name="dynamically-size-data-flow-compute-at-runtime"></a>Ridimensionare dinamicamente il calcolo del flusso di dati in fase di esecuzione

Le proprietà conteggio core e tipo di calcolo possono essere impostate in modo dinamico per adattarsi alle dimensioni dei dati di origine in ingresso in fase di esecuzione. Usare attività pipeline come Lookup o Get Metadata per individuare le dimensioni dei dati del set di dati di origine. Quindi, utilizzare Aggiungi contenuto dinamico nelle proprietà dell'attività flusso di dati.

![Flusso Dynamic Data](media/data-flow/dyna1.png "Flusso di dati dinamici")

[Ecco una breve esercitazione video che illustra questa tecnica](https://www.youtube.com/watch?v=jWSkJdtiJNM)

### <a name="data-flow-integration-runtime"></a>Runtime di integrazione del flusso di dati

Scegliere la Integration Runtime da usare per l'esecuzione dell'attività flusso di dati. Per impostazione predefinita, Data Factory utilizzerà il runtime di integrazione di Azure per la risoluzione automatica con quattro core del ruolo di lavoro e nessun TTL (time to Live). Questo IR ha un tipo di calcolo per utilizzo generico e viene eseguito nella stessa area della factory. È possibile creare runtime di integrazione di Azure personalizzati che definiscono aree specifiche, tipo di calcolo, conteggi core e TTL per l'esecuzione dell'attività flusso di dati.

Per le esecuzioni di pipeline, il cluster è un cluster di processi che richiede alcuni minuti per l'avvio prima dell'avvio dell'esecuzione. Se non viene specificato alcun valore TTL, questo tempo di avvio è necessario per ogni esecuzione della pipeline. Se si specifica un valore TTL, un pool di cluster caldo resterà attivo per il tempo specificato dopo l'ultima esecuzione, ottenendo tempi di avvio più brevi. Se, ad esempio, si dispone di un valore TTL di 60 minuti ed è necessario eseguirvi un flusso di dati una volta all'ora, il pool di cluster resterà attivo. Per altre informazioni, vedere [runtime di integrazione di Azure](concepts-integration-runtime.md).

![Azure Integration Runtime](media/data-flow/ir-new.png "Azure Integration Runtime")

> [!IMPORTANT]
> La Integration Runtime selezione nell'attività flusso di dati si applica solo alle *esecuzioni attivate* della pipeline. Il debug della pipeline con flussi di dati viene eseguito nel cluster specificato nella sessione di debug.

### <a name="polybase"></a>PolyBase

Se si usa un'analisi delle sinapsi di Azure (in precedenza SQL Data Warehouse) come sink o origine, è necessario scegliere un percorso di gestione temporanea per il carico batch di base. La polibase consente il caricamento batch in blocco anziché il caricamento dei dati riga per riga. La polibase riduce drasticamente il tempo di caricamento in Azure sinapsi Analytics.

## <a name="parameterizing-data-flows"></a>Flussi di dati parametrizzazione

### <a name="parameterized-datasets"></a>Set di impostazioni con parametri

Se il flusso di dati utilizza set di dati con parametri, impostare i valori dei parametri nella scheda **Impostazioni** .

![Esegui parametri flusso di dati](media/data-flow/params.png "Parametri")

### <a name="parameterized-data-flows"></a>Flussi di dati con parametri

Se il flusso di dati è parametrizzato, impostare i valori dinamici dei parametri del flusso di dati nella scheda **parametri** . È possibile utilizzare il linguaggio delle espressioni della pipeline di ADF o il linguaggio delle espressioni del flusso di dati per assegnare valori di parametri dinamici o letterali. Per ulteriori informazioni, vedere [parametri del flusso di dati](parameters-data-flow.md).

### <a name="parameterized-compute-properties"></a>Proprietà di calcolo con parametri.

È possibile parametrizzare il numero di core o il tipo di calcolo se si usa il runtime di integrazione di Azure per la risoluzione automatica e si specificano i valori per compute. coreCount e COMPUTE. computeType.

![Esempio di parametro di esecuzione del flusso di dati](media/data-flow/parameterize-compute.png "Esempio di parametro")

## <a name="pipeline-debug-of-data-flow-activity"></a>Debug della pipeline dell'attività flusso di dati

Per eseguire una pipeline di debug eseguita con un'attività flusso di dati, è necessario attivare la modalità di debug del flusso di dati tramite il dispositivo di scorrimento **debug del flusso di dati** nella barra superiore. La modalità di debug consente di eseguire il flusso di dati in un cluster Spark attivo. Per altre informazioni, vedere [Modalità di debug](concepts-data-flow-debug-mode.md).

![Pulsante debug](media/data-flow/debugbutton.png "Pulsante debug")

La pipeline di debug viene eseguita sul cluster di debug attivo, non sull'ambiente di Integration runtime specificato nelle impostazioni dell'attività flusso di dati. È possibile scegliere l'ambiente di calcolo di debug quando si avvia la modalità di debug.

## <a name="monitoring-the-data-flow-activity"></a>Monitoraggio dell'attività flusso di dati

L'attività flusso di dati offre un'esperienza di monitoraggio speciale in cui è possibile visualizzare le informazioni sul partizionamento, sulla fase temporale e sulla derivazione dei dati. Aprire il riquadro Monitoraggio usando l'icona degli occhiali in **azioni**. Per altre informazioni, vedere [monitoraggio dei flussi di dati](concepts-data-flow-monitoring.md).

### <a name="use-data-flow-activity-results-in-a-subsequent-activity"></a>Usa l'attività flusso di dati restituisce un'attività successiva

L'attività flusso di dati restituisce le metriche relative al numero di righe scritte in ogni sink e righe lette da ogni origine. Questi risultati vengono restituiti nella `output` sezione del risultato dell'esecuzione dell'attività. Le metriche restituite sono nel formato del codice JSON seguente.

``` json
{
    "runStatus": {
        "metrics": {
            "<your sink name1>": {
                "rowsWritten": <number of rows written>,
                "sinkProcessingTime": <sink processing time in ms>,
                "sources": {
                    "<your source name1>": {
                        "rowsRead": <number of rows read>
                    },
                    "<your source name2>": {
                        "rowsRead": <number of rows read>
                    },
                    ...
                }
            },
            "<your sink name2>": {
                ...
            },
            ...
        }
    }
}
```

Ad esempio, per ottenere il numero di righe scritte in un sink denominato "sink1" in un'attività denominata "dataflowActivity", usare `@activity('dataflowActivity').output.runStatus.metrics.sink1.rowsWritten` .

Per ottenere il numero di righe lette da un'origine denominata ' source1' utilizzata in tale sink, utilizzare `@activity('dataflowActivity').output.runStatus.metrics.sink1.sources.source1.rowsRead` .

> [!NOTE]
> Se un sink contiene zero righe scritte, non verrà visualizzato nelle metriche. L'esistenza può essere verificata utilizzando la `contains` funzione. Ad esempio, `contains(activity('dataflowActivity').output.runStatus.metrics, 'sink1')` verificherà se le righe sono state scritte in sink1.

## <a name="next-steps"></a>Passaggi successivi

Vedere attività del flusso di controllo supportate da Data Factory: 

- [Attività della condizione If](control-flow-if-condition-activity.md)
- [Eseguire l'attività di pipeline](control-flow-execute-pipeline-activity.md)
- [Per ogni attività](control-flow-for-each-activity.md)
- [Ottenere attività di metadati](control-flow-get-metadata-activity.md)
- [Attività Lookup](control-flow-lookup-activity.md)
- [Attività Web](control-flow-web-activity.md)
- [Attività Until](control-flow-until-activity.md)

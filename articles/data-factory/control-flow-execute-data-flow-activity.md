---
title: Attività flusso di datiData Flow activity
description: Come eseguire flussi di dati dall'interno di una pipeline di data factory.
services: data-factory
documentationcenter: ''
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: makromer
ms.date: 03/16/2020
ms.openlocfilehash: 115cb3e499117457629e130b6432a1cbc2224edb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79463051"
---
# <a name="data-flow-activity-in-azure-data-factory"></a>Data Flow activity in Azure Data Factory

Usare l'attività Flusso di dati per trasformare e spostare i dati tramite il mapping dei flussi di dati. Se non si ha familiarità con i flussi di dati, vedere Panoramica del [mapping dei flussi](concepts-data-flow-overview.md) di datiIf you're new to data flows, see Mapping Data Flow overview

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
Flusso | Riferimento al flusso di dati in esecuzione | DataFlowReference (Riferimento a i flussi di dati | Sì
integrationRuntime (integrazione Runtime) | Ambiente di calcolo in cui viene eseguito il flusso di dati. Se non specificato, verrà usato il runtime di integrazione di Azure con risoluzione automaticaIf not specified, the auto-resolve Azure Integration runtime will be used | IntegrationRuntimeReference | No
compute.coreCount (conteggio calcolo) | Numero di core utilizzati nel cluster di spark. Può essere specificato solo se viene usato il runtime di integrazione di Azure con risoluzione automaticaCan only be specified if the auto-resolve Azure Integration runtime is used | 8, 16, 32, 48, 80, 144, 272 | No
compute.computeType (tipo compute.computeType) | Tipo di calcolo utilizzato nel cluster di spark. Può essere specificato solo se viene usato il runtime di integrazione di Azure con risoluzione automaticaCan only be specified if the auto-resolve Azure Integration runtime is used | "Generale", "ComputeOptimized", "MemoryOptimized" | No
staging.linkedService (servizio in gestione temporanea.linkedService) | Se si usa un'origine o un sink SQL DW, l'account di archiviazione usato per PolyBase staging | LinkedServiceReference (Riferimento servizio collegato) | Solo se il flusso di dati legge o scrive in un
staging.folderPath (percorso e gestione temporanea) | Se si usa un'origine o un sink di SQL DW, il percorso della cartella nell'account di archiviazione BLOB usato per la gestione temporanea PolyBaseIf you're using a SQL DW source or sink, the folder path in blob storage account used for PolyBase staging | string | Solo se il flusso di dati legge o scrive in un

![Esecuzione del flusso di dati](media/data-flow/activity-data-flow.png "Esecuzione del flusso di dati")

### <a name="dynamically-size-data-flow-compute-at-runtime"></a>Ridimensionare dinamicamente il calcolo del flusso di dati in fase di esecuzioneDynamically size data flow compute at runtime

Le proprietà Core Count e Compute Type possono essere impostate dinamicamente per adattarsi alle dimensioni dei dati di origine in ingresso in fase di esecuzione. Usare le attività della pipeline, ad esempio Ricerca o Ottieni metadati, per trovare le dimensioni dei dati del set di dati di origine. Quindi, utilizzare Aggiungi contenuto dinamico nelle proprietà dell'attività Flusso di dati.

![Flusso dinamico dei dati](media/data-flow/dyna1.png "Flusso di dati dinamici")

[Ecco un breve video tutorial che spiega questa tecnica](https://www.youtube.com/watch?v=jWSkJdtiJNM)

### <a name="data-flow-integration-runtime"></a>Runtime di integrazione del flusso di datiData Flow integration runtime

Scegliere il runtime di integrazione da usare per l'esecuzione dell'attività Flusso di dati. Per impostazione predefinita, Data Factory utilizzerà il runtime di integrazione di Azure con quattro core di lavoro e nessun tempo di durata (TTL). Questo codice A/R ha un tipo di calcolo generico e viene eseguito nella stessa area della factory. È possibile creare runtime di integrazione di Azure personalizzati che definiscono aree specifiche, tipo di calcolo, conteggi core e TTL per l'esecuzione dell'attività del flusso di dati.

Per le esecuzioni della pipeline, il cluster è un cluster di processi che richiede alcuni minuti per l'avvio prima dell'avvio dell'esecuzione. Se non viene specificato alcun valore TTL, questo tempo di avvio è necessario per ogni esecuzione della pipeline. Se si specifica un valore TTL, un pool di cluster attivo rimarrà attivo per il tempo specificato dopo l'ultima esecuzione, con conseguenti tempi di avvio più brevi. Ad esempio, se si dispone di un valore TTL di 60 minuti ed è possibile eseguirvi un flusso di dati una volta all'ora, il pool di cluster rimarrà attivo. Per altre informazioni, vedere Runtime di integrazione di [Azure.For](concepts-integration-runtime.md)more information, see Azure integration runtime.

![Runtime di integrazione di AzureAzure Integration Runtime](media/data-flow/ir-new.png "Runtime di integrazione di AzureAzure Integration Runtime")

> [!NOTE]
> La selezione del runtime di integrazione nell'attività Flusso di dati si applica solo alle *esecuzioni attivate* della pipeline. Il debug della pipeline con i flussi di dati viene eseguito nel cluster specificato nella sessione di debug.

### <a name="polybase"></a>PolyBase

Se si usa un data warehouse SQL di Azure come sink o origine, è necessario scegliere un percorso di gestione temporanea per il caricamento batch PolyBase.If you're using an Azure SQL Data Warehouse as a sink or source, you must choose a staging location for your PolyBase batch load. PolyBase consente il caricamento in blocco di batch anziché caricare i dati riga per riga. PolyBase riduce drasticamente il tempo di caricamento in SQL DW.

## <a name="parameterizing-data-flows"></a>Parametrità dei flussi di datiParameterizing Data Flows

### <a name="parameterized-datasets"></a>Set di dati con parametriParameterized datasets

Se il flusso di dati utilizza set di dati con parametri, impostare i valori dei parametri nella scheda **Impostazioni.If** your data flow uses parameterized datasets, set the parameter values in the Settings tab.

![Esecuzione dei parametri del flusso di dati](media/data-flow/params.png "Parametri")

### <a name="parameterized-data-flows"></a>Flussi di dati con parametriParameterized data flows

Se il flusso di dati è parametrizzato, impostare i valori dinamici dei parametri del flusso di dati nella scheda **Parametri.** È possibile usare il linguaggio delle espressioni della pipeline ADF (solo per i tipi String) o il linguaggio delle espressioni del flusso di dati per assegnare valori di parametro dinamici o letterali. Per ulteriori informazioni, vedere [Parametri del flusso di dati](parameters-data-flow.md).

![Esempio di esempio di parametro Esecuzione flusso di dati](media/data-flow/parameter-example.png "Esempio di parametro")

### <a name="parameterized-compute-properties"></a>Proprietà di calcolo con parametri.

È possibile parametrizzare il numero di core o il tipo di calcolo se si usa il runtime di integrazione di Azure con risoluzione automatica e si specificano valori per compute.coreCount e compute.computeType.You can parameterize the core count or compute type if you use the auto-resolve Azure Integration runtime and specify values for compute.coreCount and compute.computeType.

![Esempio di esempio di parametro Esecuzione flusso di dati](media/data-flow/parameterize-compute.png "Esempio di parametro")

## <a name="pipeline-debug-of-data-flow-activity"></a>Debug della pipeline dell'attività Flusso di datiPipeline debug of Data Flow activity

Per eseguire una pipeline di debug eseguita con un'attività Flusso di dati, è necessario attivare la modalità di debug del flusso di dati tramite il dispositivo di scorrimento **Debug flusso** di dati sulla barra superiore. La modalità di debug consente di eseguire il flusso di dati su un cluster Spark attivo. Per ulteriori informazioni, vedere [Modalità debug](concepts-data-flow-debug-mode.md).

![Pulsante Debug](media/data-flow/debugbutton.png "Pulsante Debug")

La pipeline di debug viene eseguita sul cluster di debug attivo, non sull'ambiente di runtime di integrazione specificato nelle impostazioni dell'attività Flusso di dati. È possibile scegliere l'ambiente di calcolo di debug quando si avvia la modalità di debug.

## <a name="monitoring-the-data-flow-activity"></a>Monitoraggio dell'attività del flusso di datiMonitoring the Data Flow activity

L'attività Flusso di dati ha un'esperienza di monitoraggio speciale in cui è possibile visualizzare il partizionamento, il tempo di fase e le informazioni sulla derivazione dei dati. Aprire il riquadro di monitoraggio tramite l'icona degli occhiali in **Azioni**. Per ulteriori informazioni, vedere [Monitoraggio dei flussi](concepts-data-flow-monitoring.md)di dati .

### <a name="use-data-flow-activity-results-in-a-subsequent-activity"></a>Usare i risultati dell'attività flusso di dati in un'attività successivaUse Data Flow activity results in a subsequent activity

L'attività del flusso di dati restituisce metriche relative al numero di righe scritte in ogni sink e righe lette da ogni origine. Questi risultati vengono restituiti nella `output` sezione del risultato dell'esecuzione dell'attività. Le metriche restituite sono nel formato json riportato di seguito.

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

Ad esempio, per arrivare al numero di righe scritte in un sink denominato 'sink1' in un'attività denominata 'dataflowActivity', utilizzare `@activity('dataflowActivity').output.runStatus.metrics.sink1.rowsWritten`.

Per ottenere il numero di righe lette da un'origine denominata `@activity('dataflowActivity').output.runStatus.metrics.sink1.sources.source1.rowsRead`'source1' utilizzata in tale sink, utilizzare .

> [!NOTE]
> Se un sink ha zero righe scritte, non verrà visualizzato nelle metriche. L'esistenza può `contains` essere verificata utilizzando la funzione. Ad esempio, `contains(activity('dataflowActivity').output.runStatus.metrics, 'sink1')` controllerà se sono state scritte righe in sink1.

## <a name="next-steps"></a>Passaggi successivi

Vedere le attività del flusso di controllo supportate da Data Factory:See control flow activities supported by Data Factory: 

- [Attività della condizione If](control-flow-if-condition-activity.md)
- [Attività Esegui pipeline](control-flow-execute-pipeline-activity.md)
- [Per ogni attività](control-flow-for-each-activity.md)
- [Ottenere l'attività dei metadatiGet Metadata Activity](control-flow-get-metadata-activity.md)
- [Attività Lookup](control-flow-lookup-activity.md)
- [Attività Web](control-flow-web-activity.md)
- [Attività Until](control-flow-until-activity.md)

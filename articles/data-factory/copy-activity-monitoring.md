---
title: Monitorare l'attività di copia
description: Informazioni su come monitorare l'esecuzione dell'attività di copia in Azure Data Factory.Learn about how to monitor the copy activity execution in Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: jingwang
ms.openlocfilehash: 47824095e892ca3c919d2d871feb612758ab2308
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417845"
---
# <a name="monitor-copy-activity"></a>Monitorare l'attività di copia

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo illustra come monitorare l'esecuzione dell'attività di copia in Azure Data Factory.This article outlines how to monitor the copy activity execution in Azure Data Factory. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale sull'attività di copia.

## <a name="monitor-visually"></a>Monitorare visivamente

Dopo aver creato e pubblicato una pipeline in Azure Data Factory, è possibile associarla a un trigger o avviare manualmente un'esecuzione ad hoc. È possibile monitorare tutte le esecuzioni della pipeline in modo nativo nell'esperienza utente di Azure Data Factory.You can monitor all of your pipeline runs natively in the Azure Data Factory user experience. Per informazioni sul monitoraggio di Azure Data Factory in [generale, vedere Monitorare visivamente Azure Data Factory.](monitor-visually.md)

Per monitorare l'esecuzione dell'attività Copia, passare all'interfaccia utente **Author & Monitor** della data factory. Nella scheda **Monitoraggio** viene visualizzato un elenco di esecuzioni della pipeline, fare clic sul collegamento **del nome** della pipeline per accedere all'elenco di esecuzioni di attività nell'esecuzione della pipeline.

![Monitorare l'esecuzione dell'attività di copiaMonitor copy activity run](./media/copy-activity-overview/monitor-pipeline-run.png)

A questo livello, è possibile visualizzare i collegamenti per copiare l'input dell'attività, l'output e gli errori (se l'esecuzione dell'attività di copia ha esito negativo), nonché statistiche come durata/stato. Facendo clic sul pulsante **Dettagli** (occhiali) accanto al nome dell'attività di copia verranno forniti dettagli dettagliati sull'esecuzione dell'attività di copia. 

![Monitorare l'esecuzione dell'attività di copiaMonitor copy activity run](./media/copy-activity-overview/monitor-copy-activity-run.png)

In questa visualizzazione di monitoraggio grafico, Azure Data Factory presenta le informazioni sull'esecuzione dell'attività di copia, inclusi i dati letti/volumi scritti, il numero di file/righe di dati copiati dall'origine al sink, la velocità effettiva, le configurazioni applicate per lo scenario di copia, i passaggi dell'attività di copia con le durate e i dettagli corrispondenti e altro ancora. Fare riferimento a [questa tabella](#monitor-programmatically) su ogni metrica possibile e la relativa descrizione dettagliata. 

In alcuni scenari, quando si esegue un'attività di copia in Data Factory, nella parte superiore della visualizzazione di monitoraggio dell'attività di copia viene visualizzato "Suggerimenti per **l'ottimizzazione delle prestazioni"** nella parte superiore della visualizzazione di monitoraggio dell'attività di copia, come illustrato nell'esempio. I suggerimenti indicano il collo di bottiglia identificato da ADF per l'esecuzione di copia specifica, insieme a suggerimenti su cosa modificare per aumentare la velocità effettiva della copia. Ulteriori informazioni sui [suggerimenti per l'ottimizzazione automatica delle prestazioni](copy-activity-performance-troubleshooting.md#performance-tuning-tips).

I **dettagli e le durate** di esecuzione inferiori descrivono i passaggi chiave eseguiti dall'attività di copia, il che è particolarmente utile per la risoluzione delle prestazioni di copia. Il collo di bottiglia dell'esecuzione della copia è quello con la durata più lunga. Fare riferimento a [Risolvere i problemi relativi alle prestazioni dell'attività](copy-activity-performance-troubleshooting.md) di copia per informazioni su ciò che ogni fase rappresenta e le indicazioni dettagliate per la risoluzione dei problemi.

**Esempio: copia da Amazon S3 ad Azure Data Lake Storage Gen2**

![Monitorare i dettagli dell'esecuzione dell'attività di copiaMonitor copy activity run details](./media/copy-activity-overview/monitor-copy-activity-run-details.png)

## <a name="monitor-programmatically"></a>Monitorare a livello di codice

I dettagli di esecuzione dell'attività di copia e le caratteristiche delle prestazioni vengono restituiti anche nella sezione**Output** del **risultato** > dell'esecuzione dell'attività di copia, usata per eseguire il rendering della visualizzazione di monitoraggio dell'interfaccia utente. Di seguito è riportato un elenco completo delle proprietà che potrebbero essere restituite. Verranno visualizzate solo le proprietà applicabili allo scenario di copia. Per informazioni su come monitorare le esecuzioni di attività a livello di codice in generale, vedere Monitorare a livello di codice una data factory di Azure.For information about how to monitor activity runs programmatically in general, see [Programmatically monitor an Azure data factory](monitor-programmatically.md).

| Nome proprietà  | Descrizione | Unità in uscita |
|:--- |:--- |:--- |
| dataRead | Quantità effettiva di dati letti dall'origine. | Valore Int64, in byte |
| dataWritten | Montaggio effettivo dei dati scritti/commit nel sink. Le dimensioni possono `dataRead` essere diverse dalle dimensioni, in quanto si riferiscono al modo in cui ogni archivio dati archivia i dati. | Valore Int64, in byte |
| filesRead | Il numero di file letti dall'origine basata su file. | Valore Int64 (senza unità) |
| filesWritten | Numero di file scritti o di cui è stato eseguito il commit nel sink basato su file. | Valore Int64 (senza unità) |
| sourcePeakConnections (informazioni in base alle connessioni sourcePeak | Numero massimo di connessioni simultanee stabilite all'archivio dati di origine durante l'esecuzione dell'attività di copia. | Valore Int64 (senza unità) |
| sinkPeakConnections (sinkPeakConnections) | Numero massimo di connessioni simultanee stabilito all'archivio dati sink durante l'esecuzione dell'attività di copia. | Valore Int64 (senza unità) |
| righeLettura | Numero di righe lette dall'origine (non applicabile per la copia binaria). | Valore Int64 (senza unità) |
| rowsCopied | Numero di righe copiate nel sink (non applicabile per la copia binaria). | Valore Int64 (senza unità) |
| rowsSkipped | Numero di righe incompatibili ignorate. È possibile abilitare l'ignoranza delle `enableSkipIncompatibleRow` righe incompatibili impostandola su true. | Valore Int64 (senza unità) |
| copyDuration | Durata dell'esecuzione della copia. | Valore Int32, in secondi |
| throughput | Velocità di trasferimento dei dati. | Numero a virgola mobile, in KBps |
| sourcePeakConnections (informazioni in base alle connessioni sourcePeak | Numero massimo di connessioni simultanee stabilite all'archivio dati di origine durante l'esecuzione dell'attività di copia. | Valore Int32 (nessuna unità) |
| sinkPeakConnections (sinkPeakConnections)| Numero massimo di connessioni simultanee stabilito all'archivio dati sink durante l'esecuzione dell'attività di copia.| Valore Int32 (nessuna unità) |
| sqlDwPolyBase | Se PolyBase viene utilizzato quando i dati vengono copiati in SQL Data Warehouse. | Boolean |
| redshiftUnload | Se UNLOAD viene utilizzato quando i dati vengono copiati da Redshift. | Boolean |
| hdfsDistcp | Se DistCp viene utilizzato quando i dati vengono copiati da HDFS. | Boolean |
| effectiveIntegrationRuntime | Il runtime di integrazione (IR) o i runtime `<IR name> (<region if it's Azure IR>)`utilizzati per alimentare l'esecuzione dell'attività, nel formato . | Testo (stringa) |
| usedDataIntegrationUnits | Le unità di integrazione dati effettive durante la copia. | Valore Int32 |
| usedParallelCopies | Proprietà parallelCopies effettiva durante la copia. | Valore Int32 |
| redirectRowPath | Percorso del log delle righe incompatibili ignorate nell'archivio BLOB configurato nella `redirectIncompatibleRowSettings` proprietà. Vedere [Tolleranza di errore](copy-activity-overview.md#fault-tolerance). | Testo (stringa) |
| executionDetails | Ulteriori dettagli sulle fasi dell'attività di copia e sui passaggi, le durate, le configurazioni e così via corrispondenti. Non è consigliabile analizzare questa sezione perché potrebbe cambiare. Per comprendere meglio in che modo consente di comprendere e risolvere i problemi relativi alle prestazioni di copia, fare riferimento alla sezione [Monitor visivamente.](#monitor-visually) | Array |
| perfRecommendation | Suggerimenti per l'ottimizzazione delle prestazioni di copia. Per informazioni dettagliate, vedere Suggerimenti per [l'ottimizzazione delle prestazioni.](copy-activity-performance-troubleshooting.md#performance-tuning-tips) | Array |

**Esempio:**

```json
"output": {
    "dataRead": 1180089300500,
    "dataWritten": 1180089300500,
    "filesRead": 110,
    "filesWritten": 110,
    "sourcePeakConnections": 640,
    "sinkPeakConnections": 1024,
    "copyDuration": 388,
    "throughput": 2970183,
    "errors": [],
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)",
    "usedDataIntegrationUnits": 128,
    "billingReference": "{\"activityType\":\"DataMovement\",\"billableDuration\":[{\"Managed\":11.733333333333336}]}",
    "usedParallelCopies": 64,
    "executionDetails": [
        {
            "source": {
                "type": "AmazonS3"
            },
            "sink": {
                "type": "AzureBlobFS",
                "region": "East US",
                "throttlingErrors": 6
            },
            "status": "Succeeded",
            "start": "2020-03-04T02:13:25.1454206Z",
            "duration": 388,
            "usedDataIntegrationUnits": 128,
            "usedParallelCopies": 64,
            "profile": {
                "queue": {
                    "status": "Completed",
                    "duration": 2
                },
                "transfer": {
                    "status": "Completed",
                    "duration": 386,
                    "details": {
                        "listingSource": {
                            "type": "AmazonS3",
                            "workingDuration": 0
                        },
                        "readingFromSource": {
                            "type": "AmazonS3",
                            "workingDuration": 301
                        },
                        "writingToSink": {
                            "type": "AzureBlobFS",
                            "workingDuration": 335
                        }
                    }
                }
            },
            "detailedDurations": {
                "queuingDuration": 2,
                "transferDuration": 386
            }
        }
    ],
    "perfRecommendation": [
        {
            "Tip": "6 write operations were throttled by the sink data store. To achieve better performance, you are suggested to check and increase the allowed request rate for Azure Data Lake Storage Gen2, or reduce the number of concurrent copy runs and other data access, or reduce the DIU or parallel copy.",
            "ReferUrl": "https://go.microsoft.com/fwlink/?linkid=2102534 ",
            "RuleName": "ReduceThrottlingErrorPerfRecommendationRule"
        }
    ],
    "durationInQueue": {
        "integrationRuntimeQueue": 0
    }
}
```

## <a name="next-steps"></a>Passaggi successivi
Vedere gli altri articoli relativi all'attività di copia:

\-[Panoramica dell'attività di copia](copy-activity-overview.md)

\- [Prestazioni dell'attività di copia](copy-activity-performance.md)
---
title: Monitorare l'attività di copia
description: Informazioni su come monitorare l'esecuzione dell'attività di copia in Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/08/2020
ms.author: jingwang
ms.openlocfilehash: 4e7828810a069756d1a0cde55ab47915ad11acc5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85249704"
---
# <a name="monitor-copy-activity"></a>Monitorare l'attività di copia

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo illustra come monitorare l'esecuzione dell'attività di copia in Azure Data Factory. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale sull'attività di copia.

## <a name="monitor-visually"></a>Monitorare visivamente

Dopo aver creato e pubblicato una pipeline in Azure Data Factory, è possibile associarla a un trigger o avviare manualmente un'esecuzione ad hoc. È possibile monitorare tutte le esecuzioni della pipeline in modo nativo nell'esperienza utente di Azure Data Factory. Informazioni sul monitoraggio Azure Data Factory in generale dal monitoraggio [visivo Azure Data Factory](monitor-visually.md).

Per monitorare l'esecuzione dell'attività di copia, passare all'interfaccia utente di data factory **& monitoraggio** . Nella scheda **monitoraggio** viene visualizzato un elenco di esecuzioni di pipeline, fare clic sul collegamento **nome pipeline** per accedere all'elenco delle esecuzioni di attività nell'esecuzione della pipeline.

![Monitorare l'esecuzione dell'attività di copia](./media/copy-activity-overview/monitor-pipeline-run.png)

A questo livello, è possibile visualizzare i collegamenti all'input dell'attività di copia, l'output e gli errori (se l'esecuzione dell'attività di copia ha esito negativo), oltre a statistiche come Duration/status. Facendo clic sul pulsante **Dettagli** (occhiali) accanto al nome dell'attività di copia, vengono illustrati i dettagli approfonditi sull'esecuzione dell'attività di copia. 

![Monitorare l'esecuzione dell'attività di copia](./media/copy-activity-overview/monitor-copy-activity-run.png)

In questa visualizzazione di monitoraggio con interfaccia grafica, Azure Data Factory presenta le informazioni di esecuzione dell'attività di copia, inclusi il volume di lettura/scrittura dei dati, il numero di file o righe di dati copiati dall'origine al sink, la velocità effettiva, le configurazioni applicate per lo scenario di copia, i passaggi dell'attività di copia con le durate e i dettagli corrispondenti e altro ancora. Fare riferimento a [questa tabella](#monitor-programmatically) per ogni metrica possibile e la relativa descrizione dettagliata. 

In alcuni scenari, quando si esegue un'attività di copia in Data Factory, verranno visualizzati **"suggerimenti per l'ottimizzazione delle prestazioni"** nella parte superiore della visualizzazione monitoraggio dell'attività di copia, come illustrato nell'esempio. I suggerimenti indicano il collo di bottiglia identificato da ADF per l'esecuzione della copia specifica, oltre a suggerimenti sugli elementi da modificare per aumentare la velocità effettiva della copia. Altre informazioni sui [Suggerimenti per l'ottimizzazione automatica delle prestazioni](copy-activity-performance-troubleshooting.md#performance-tuning-tips).

I **Dettagli di esecuzione e le durate** inferiori descrivono i passaggi chiave che l'attività di copia passa, che è particolarmente utile per la risoluzione dei problemi relativi alle prestazioni di copia. Il collo di bottiglia dell'esecuzione della copia è quello con la durata più lunga. Fare riferimento a [risolvere i problemi relativi alle prestazioni dell'attività di copia](copy-activity-performance-troubleshooting.md) in per ogni fase rappresentata e istruzioni dettagliate per la risoluzione dei problemi.

**Esempio: copia da Amazon S3 a Azure Data Lake Storage Gen2**

![Monitorare i dettagli dell'esecuzione dell'attività di copia](./media/copy-activity-overview/monitor-copy-activity-run-details.png)

## <a name="monitor-programmatically"></a>Monitorare a livello di codice

I dettagli dell'esecuzione dell'attività di copia e le caratteristiche delle prestazioni vengono restituiti anche nella sezione output dell' **esecuzione dell'attività di copia**  >  **Output** , che viene utilizzata per eseguire il rendering della visualizzazione di monitoraggio dell'interfaccia utente. Di seguito è riportato un elenco completo delle proprietà che possono essere restituite. Verranno visualizzate solo le proprietà applicabili allo scenario di copia. Per informazioni sul monitoraggio delle esecuzioni di attività a livello di programmazione in generale, vedere [monitorare un data factory di Azure a livello di codice](monitor-programmatically.md).

| Nome proprietà  | Descrizione | Unità nell'output |
|:--- |:--- |:--- |
| dataRead | Quantità effettiva di dati letti dall'origine. | Valore Int64, in byte |
| dataWritten | Montaggio effettivo dei dati scritti/sottoposte a commit nel sink. Le dimensioni possono essere diverse dalla `dataRead` dimensione, in quanto in questo modo viene correlato il modo in cui ogni archivio dati archivia i dati. | Valore Int64, in byte |
| filesRead | Numero di file letti dall'origine basata su file. | Valore Int64 (senza unità) |
| filesWritten | Numero di file scritti/sottoposte a commit nel sink basato su file. | Valore Int64 (senza unità) |
| sourcePeakConnections | Numero massimo di connessioni simultanee stabilite nell'archivio dati di origine durante l'esecuzione dell'attività di copia. | Valore Int64 (senza unità) |
| sinkPeakConnections | Numero massimo di connessioni simultanee stabilite nell'archivio dati sink durante l'esecuzione dell'attività di copia. | Valore Int64 (senza unità) |
| rowsRead | Numero di righe lette dall'origine. Questa metrica non si applica quando si copiano i file così come sono senza analizzarli, ad esempio quando i set di dati di origine e sink sono di tipo binario o di altro tipo di formato con impostazioni identiche. | Valore Int64 (senza unità) |
| rowsCopied | Numero di righe copiate nel sink. Questa metrica non si applica quando si copiano i file così come sono senza analizzarli, ad esempio quando i set di dati di origine e sink sono di tipo binario o di altro tipo di formato con impostazioni identiche.  | Valore Int64 (senza unità) |
| rowsSkipped | Numero di righe non compatibili ignorate. È possibile abilitare le righe incompatibili da ignorare impostando `enableSkipIncompatibleRow` su true. | Valore Int64 (senza unità) |
| copyDuration | Durata dell'esecuzione della copia. | Valore Int32, in secondi |
| throughput | Frequenza di trasferimento dei dati. | Numero a virgola mobile, in KBps |
| sourcePeakConnections | Numero massimo di connessioni simultanee stabilite nell'archivio dati di origine durante l'esecuzione dell'attività di copia. | Valore Int32 (nessuna unità) |
| sinkPeakConnections| Numero massimo di connessioni simultanee stabilite nell'archivio dati sink durante l'esecuzione dell'attività di copia.| Valore Int32 (nessuna unità) |
| sqlDwPolyBase | Indica se la polibase viene utilizzata quando i dati vengono copiati in SQL Data Warehouse. | Boolean |
| redshiftUnload | Indica se lo SCARICAmento viene utilizzato quando i dati vengono copiati da spostamento verso il suo | Boolean |
| hdfsDistcp | Indica se DistCp viene usato quando i dati vengono copiati da HDFS. | Boolean |
| effectiveIntegrationRuntime | Runtime di integrazione (IR) o Runtime usati per potenziare l'esecuzione dell'attività, nel formato `<IR name> (<region if it's Azure IR>)` . | Testo (stringa) |
| usedDataIntegrationUnits | Le unità di integrazione dati effettive durante la copia. | Valore Int32 |
| usedParallelCopies | Proprietà parallelCopies effettiva durante la copia. | Valore Int32 |
| redirectRowPath | Percorso del log delle righe incompatibili ignorate nell'archivio BLOB configurato nella `redirectIncompatibleRowSettings` Proprietà. Vedere [tolleranza di errore](copy-activity-overview.md#fault-tolerance). | Testo (stringa) |
| executionDetails | Ulteriori dettagli sulle fasi dell'attività di copia e sui passaggi, le durate, le configurazioni e così via corrispondenti. Non è consigliabile analizzare questa sezione perché potrebbe cambiare. Per comprendere meglio il modo in cui è possibile comprendere e risolvere i problemi relativi alle prestazioni di copia, vedere la sezione [monitoraggio visivo](#monitor-visually) . | Array |
| perfRecommendation | Copia suggerimenti per l'ottimizzazione delle prestazioni. Per informazioni dettagliate, vedere Suggerimenti per l' [ottimizzazione delle prestazioni](copy-activity-performance-troubleshooting.md#performance-tuning-tips) . | Array |

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

\- [Panoramica dell'attività di copia](copy-activity-overview.md)

\- [Prestazioni dell'attività di copia](copy-activity-performance.md)
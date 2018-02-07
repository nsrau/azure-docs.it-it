---
title: "Attività di copia in Azure Data Factory | Microsoft Docs"
description: "Informazioni sull'attività di copia in Azure Data Factory che è possibile usare per copiare i dati da un archivio dati di origine a un archivio dati sink supportato."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2018
ms.author: jingwang
ms.openlocfilehash: 2095d75ed042ae8be02ae0a1570f8e77d06a3563
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/19/2018
---
# <a name="copy-activity-in-azure-data-factory"></a>Attività di copia in Azure Data Factory

## <a name="overview"></a>Panoramica

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versione 1 - Disponibilità generale](v1/data-factory-data-movement-activities.md)
> * [Versione 2 - Anteprima](copy-activity-overview.md)

In Azure Data Factory è possibile usare l'attività di copia per copiare i dati da archivi dati locali e cloud. Dopo la copia, è possibile trasformare o analizzare ulteriormente i dati. L'attività di copia può essere usata anche per pubblicare risultati di trasformazione e analisi per Business Intelligence (BI) e l'uso delle applicazioni.

![Ruolo dell'attività di copia](media/copy-activity-overview/copy-activity.png)

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere [Attività di copia nella versione 1](v1/data-factory-data-movement-activities.md).

L'attività di copia viene eseguita in un [runtime di integrazione](concepts-integration-runtime.md). Per altri scenari di copia dei dati, è possibile usare versioni diverse del runtime di integrazione:

* Quando si copiano i dati tra archivi di dati accessibili pubblicamente, l'attività di copia può essere migliorata con il **runtime di integrazione di Azure** che è sicuro, affidabile, scalabile e [disponibile a livello globale](concepts-integration-runtime.md#integration-runtime-location).
* Quando si copiano i dati da/a archivi di dati in locale o in una rete con il controllo di accesso (ad esempio, rete virtuale di Azure), è necessario configurare un **runtime di integrazione self-hosted** per migliorare la copia dei dati.

Integration Runtime deve essere associato a ogni archivio dati di origine e sink. Informazioni su come l'attività di copia [determina quale runtime di integrazione usare](concepts-integration-runtime.md#determining-which-ir-to-use).

L'attività di copia viene sottoposta alle fasi seguenti per copiare i dati dall'origine al sink. Il servizio su cui si basa l'attività di copia:

1. Legge i dati dall'archivio dati di origine.
2. Esegue la serializzazione/deserializzazione, compressione/decompressione, il mapping di colonne e così via. Esegue tali operazioni sulla base delle configurazioni del set di dati di input, del set di dati di output e dell'attività di copia.
3. Scrive i dati nell'archivio dati sink/di destinazione.

![Panoramica dell'attività di copia](media/copy-activity-overview/copy-activity-overview.png)

## <a name="supported-data-stores-and-formats"></a>Archivi dati e formati supportati

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

### <a name="supported-file-formats"></a>Formati di file supportati

È possibile usare l'attività di copia per **copiare i file così come sono** tra due archivi dati basati su file. In questo caso i dati vengono copiati in modo efficiente senza serializzazione/deserializzazione.

L'attività di copia supporta anche operazioni di lettura e scrittura di file in formati specifici, ovvero **Text, JSON, Avro, ORC e Parquet** e sono supportati i codec di compressione **GZip, Deflate, BZip2 e ZipDeflate**. Vedere [Formati di compressione e file supportati](supported-file-formats-and-compression-codecs.md) per i dettagli.

È possibile ad esempio eseguire queste attività di copia:

* Copiare i dati nell'istanza locale di SQL Server e scrivere in Azure Data Lake Store nel formato ORC.
* Copiare i file in formato testo (CSV) dal file system locale e scrivere nel BLOB di Azure nel formato Avro.
* Copiare i file compressi dal file system locale e decomprimerli in Azure Data Lake Store.
* Copiare i dati in formato testo (CSV) con compressione GZip dal BLOB di Azure e scrivere nel database SQL Azure.

## <a name="supported-regions"></a>Aree supportate

Tuttavia, il servizio alla base dell'attività di copia è disponibile a livello globale nelle aree geografiche riportate nella sezione [Località del runtime di integrazione](concepts-integration-runtime.md#integration-runtime-location). La topologia disponibile a livello globale garantisce uno spostamento di dati efficiente e di solito consente di evitare passaggi tra diverse aree. Per la disponibilità del servizio Data Factory e lo spostamento dei dati in un'area, vedere [Servizi in base all'area](https://azure.microsoft.com/regions/#services) .

## <a name="configuration"></a>Configurazione

Per usare l'attività di copia in Azure Data Factory, è necessario:

1. **Creare servizi collegati per gli archivi dati di origine e sink.** Per configurare le proprietà supportate, fare riferimento alla sezione "Proprietà del servizio collegato" dell'articolo sul connettore. È possibile trovare un elenco dei connettori supportati nella sezione relativa agli [archivi di dati e ai formati supportati](#supported-data-stores-and-formats).
2. **Creare i set di dati per origine e sink.** Per configurare le proprietà supportate, fare riferimento alla sezione "Proprietà dei set di dati" dell'articolo sui connettori di origine e sink.
3. **Creare una pipeline con attività di copia.** Nella sezione seguente viene illustrato un esempio.  

### <a name="syntax"></a>Sintassi

Nel modello seguente di un'attività di copia è incluso l'elenco completo delle proprietà supportate. Specificare quelle più adatte per il proprio scenario.

```json
"activities":[
    {
        "name": "CopyActivityTemplate",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<source dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<sink dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>",
                <properties>
            },
            "sink": {
                "type": "<sink type>"
                <properties>
            },
            "translator":
            {
                "type": "TabularTranslator",
                "columnMappings": "<column mapping>"
            },
            "cloudDataMovementUnits": <number>,
            "parallelCopies": <number>,
            "enableStaging": true/false,
            "stagingSettings": {
                <properties>
            },
            "enableSkipIncompatibleRow": true/false,
            "redirectIncompatibleRowSettings": {
                <properties>
            }
        }
    }
]
```

### <a name="syntax-details"></a>Dettagli sintassi

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type di un'attività di copia deve essere impostata su: **Copy** | Sì |
| inputs | Specificare il set di dati creato che fa riferimento ai dati di origine. L'attività di copia supporta un singolo input. | Sì |
| outputs | Specificare il set di dati creato che fa riferimento ai dati sink. L'attività di copia supporta un singolo output. | Sì |
| typeProperties | Un gruppo di proprietà per configurare l'attività di copia. | Sì |
| una sezione source | Specificare il tipo di origine della copia e le proprietà corrispondenti per recuperare i dati.<br/><br/>Vedere la sezione "Proprietà dell'attività di copia" nell'articolo sul connettore elencato in [Archivi dati e formati supportati](#supported-data-stores-and-formats). | Sì |
| sink | Specificare il tipo di sink della copia e le proprietà corrispondenti per scrivere i dati.<br/><br/>Vedere la sezione "Proprietà dell'attività di copia" nell'articolo sul connettore elencato in [Archivi dati e formati supportati](#supported-data-stores-and-formats). | Sì |
| translator | Specificare il mapping esplicito di colonne da origine a sink. Si applica quando il comportamento di copia predefinito non soddisfa le esigenze.<br/><br/>Vedere [Mapping dello schema e dei tipi di dati](copy-activity-schema-and-type-mapping.md). | No  |
| cloudDataMovementUnits | Specificare l'efficacia del [runtime di integrazione di Azure](concepts-integration-runtime.md) per migliorare la copia dei dati.<br/><br/>Vedere [Unità di spostamento dati cloud](copy-activity-performance.md). | No  |
| parallelCopies | Specificare il parallelismo che l'attività di copia deve usare durante la lettura dei dati dall'origine e la scrittura dei dati nel sink.<br/><br/>Vedere [Copia parallela](copy-activity-performance.md#parallel-copy). | No  |
| enableStaging<br/>stagingSettings | Scegliere di eseguire lo staging dei dati provvisori in un archivio BLOB anziché copiarli direttamente dall'origine al sink.<br/><br/>Informazioni sugli scenari e sui dettagli di configurazione in [Copia di staging](copy-activity-performance.md#staged-copy). | No  |
| enableSkipIncompatibleRow<br/>redirectIncompatibleRowSettings| Scegliere come gestire le righe incompatibili durante la copia di dati dall'origine al sink.<br/><br/>Vedere [Tolleranza di errore](copy-activity-fault-tolerance.md). | No  |

## <a name="monitoring"></a>Monitoraggio

È possibile monitorare l'esecuzione dell'attività di copia nell'interfaccia utente "Crea e monitora" di Azure Data Factory o a livello di codice. È quindi possibile confrontare le prestazioni e la configurazione dello scenario con le [informazioni di riferimento sulle prestazioni](copy-activity-performance.md#performance-reference) dell'attività di copia ottenute da test interni.

### <a name="monitor-visually"></a>Monitorare visivamente

Per monitorare visivamente l'esecuzione dell'attività di copia, selezionare -> **Copia e monitora** -> **scheda Monitoraggio** della data factory. Verrà visualizzato un elenco di esecuzioni di pipeline con un collegamento per la visualizzazione delle esecuzioni delle attività nella colonna **Azioni**. 

![Monitorare le esecuzioni di pipeline](./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png)

Fare clic per visualizzare l'elenco delle attività in questa esecuzione di pipeline. Nella colonna **Azioni** sono presenti collegamenti all'input e all'output dell'attività di copia, agli errori (se l'esecuzione dell'attività di copia ha esito negativo) e ai dettagli.

![Monitorare le esecuzioni delle attività](./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png)

Fare clic sul collegamento "**Dettagli**" in **Azioni** per visualizzare i dettagli di esecuzione e le caratteristiche delle prestazioni dell'attività di copia. Vengono visualizzate informazioni quali il volume, le righe e i file di dati copiati dall'origine al sink, la velocità effettiva, i passaggi eseguiti (con la durata corrispondente) e le configurazioni usate per lo scenario di copia.

**Esempio: copia da Amazon S3 ad Azure Data Lake Store**
![Dettagli esecuzione attività di monitoraggio](./media/copy-activity-overview/monitor-activity-run-details-adls.png)

**Esempio: copia dal database SQL di Azure ad Azure SQL Data Warehouse tramite una copia di gestione temporanea**
![Dettagli esecuzione attività di monitoraggio](./media/copy-activity-overview/monitor-activity-run-details-sql-dw.png)

### <a name="monitor-programmatically"></a>Monitorare a livello di codice

I dettagli dell'esecuzione dell'attività di copia e le caratteristiche relative alle prestazioni vengono restituiti anche nella sezione relativa all'output del risultato dell'esecuzione dell'attività di copia. Di seguito viene offerto l'elenco completo, ma solo i dettagli applicabili al proprio scenario di copia vengono visualizzati. Informazioni su come monitorare l'esecuzione dell'attività nella [sezione sull'avvio rapido del monitoraggio](quickstart-create-data-factory-dot-net.md#monitor-a-pipeline-run).

| Nome proprietà  | DESCRIZIONE | Unità |
|:--- |:--- |:--- |
| dataRead | Dimensioni dei dati letti nell'origine | Valore Int64 in **byte** |
| dataWritten | Dimensioni dei dati scritti nel sink | Valore Int64 in **byte** |
| filesRead | Numero di file copiati quando si copiano dati da una risorsa di archiviazione di file. | Valore Int64 (senza unità) |
| filesWritten | Numero di file copiati quando si copiano dati in una risorsa di archiviazione di file. | Valore Int64 (senza unità) |
| rowsCopied | Numero di righe copiate (non applicabile alla copia binaria). | Valore Int64 (senza unità) |
| rowsSkipped | Numero di righe incompatibili ignorate. È possibile abilitare la funzionalità impostando il valore "enableSkipIncompatibleRow" su True. | Valore Int64 (senza unità) |
| throughput | Velocità di trasferimento dei dati | Numero a virgola mobile in **KB/s** |
| copyDuration | Durata della copia | Valore Int32 in secondi |
| sqlDwPolyBase | Se PolyBase viene usato per copiare i dati in SQL Data Warehouse. | boolean |
| redshiftUnload | Se UNLOAD viene usato per copiare i dati da Redshift. | boolean |
| hdfsDistcp | Se DistCp viene usato per copiare i dati da HDFS. | boolean |
| effectiveIntegrationRuntime | Mostra gli Integration Runtime usati per migliorare l'esecuzione dell'attività, nel formato `<IR name> (<region if it's Azure IR>)`. | Testo (stringa) |
| usedCloudDataMovementUnits | Il numero effettivo di unità di spostamento dati cloud durante la copia. | Valore Int32 |
| usedParallelCopies | Proprietà parallelCopies effettiva durante la copia. | Valore Int32|
| redirectRowPath | Percorso del log delle righe incompatibili ignorate nel contenitore di archiviazione BLOB configurato in "redirectIncompatibleRowSettings". Vedere l'esempio seguente. | Testo (stringa) |
| executionDetails | Maggiori dettagli sulle fasi a cui viene sottoposta l'attività di copia e sui passaggi, sulla durata, sulle configurazioni usate e così via. Non è consigliabile analizzare questa sezione, perché può cambiare. | Array |

```json
"output": {
    "dataRead": 107280845500,
    "dataWritten": 107280845500,
    "filesRead": 10,
    "filesWritten": 10,
    "copyDuration": 224,
    "throughput": 467707.344,
    "errors": [],
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US 2)",
    "usedCloudDataMovementUnits": 32,
    "usedParallelCopies": 8,
    "executionDetails": [
        {
            "source": {
                "type": "AmazonS3"
            },
            "sink": {
                "type": "AzureDataLakeStore"
            },
            "status": "Succeeded",
            "start": "2018-01-17T15:13:00.3515165Z",
            "duration": 221,
            "usedCloudDataMovementUnits": 32,
            "usedParallelCopies": 8,
            "detailedDurations": {
                "queuingDuration": 2,
                "transferDuration": 219
            }
        }
    ]
}
```

## <a name="schema-and-data-type-mapping"></a>Mapping dello schema e dei tipi di dati

Vedere [Mapping dello schema e dei tipi di dati](copy-activity-schema-and-type-mapping.md) per informazioni su come l'attività di copia esegue il mapping dei dati di origine al sink.

## <a name="fault-tolerance"></a>Tolleranza di errore

Per impostazione predefinita, l'attività di copia interrompe la copia dei dati e restituisce un errore quando incontra dei dati incompatibili tra l'origine e il sink. È possibile applicare una configurazione che preveda di ignorare e registrare le righe incompatibili e di copiare soltanto i dati compatibili affinché la copia vada a buon fine. Per altri dettagli, vedere la [Tolleranza di errore dell'attività di copia: ignorare le righe incompatibili](copy-activity-fault-tolerance.md).

## <a name="performance-and-tuning"></a>Prestazioni e ottimizzazione

Vedere l'articolo [Guida alle prestazioni delle attività di copia e all'ottimizzazione](copy-activity-performance.md), che descrive i fattori chiave che influiscono sulle prestazioni di spostamento dei dati (attività di copia) in Data Factory di Azure. Vengono anche elencate le prestazioni osservate durante il test interni e vengono descritti i modi per ottimizzare le prestazioni dell'attività di copia.

## <a name="incremental-copy"></a>Copia incrementale 
Data Factory versione 2 supporta scenari per la copia incrementale di dati delta da un archivio dati di origine a un archivio dati di destinazione. Vedere [Esercitazione: Copiare dati in modo incrementale](tutorial-incremental-copy-overview.md). 

## <a name="read-and-write-partitioned-data"></a>Leggere e scrivere dati partizionati
Nella versione 1, Azure Data Factory supportava la lettura o la scrittura di dati partizionati usando le variabili di sistema SliceStart/SliceEnd/WindowStart/WindowEnd. Nella versione 2, è possibile ottenere questo comportamento usando un parametro della pipeline e l'ora di inizio/ora prevista per il trigger come valore per il parametro. Per altre informazioni, vedere [Come leggere o scrivere dati partizionati](how-to-read-write-partitioned-data.md).

## <a name="next-steps"></a>Passaggi successivi
Vedere le guide rapide, le esercitazioni e gli esempi seguenti:

- [Copiare i dati da una posizione a un'altra nella stessa archiviazione BLOB di Azure](quickstart-create-data-factory-dot-net.md)
- [Copiare i dati dall'archiviazione BLOB di Azure al database SQL di Azure](tutorial-copy-data-dot-net.md)
- [Copiare i dati da un'istanza di SQL Server locale ad Azure](tutorial-hybrid-copy-powershell.md)

---
title: Attività di copia in Azure Data Factory | Microsoft Docs
description: Informazioni sull'attività di copia in Azure Data Factory che è possibile usare per copiare i dati da un archivio dati di origine a un archivio dati sink supportato.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: 23ae7b5cfec26fb2483a3e4ac13a1220888d76ee
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69614259"
---
# <a name="copy-activity-in-azure-data-factory"></a>Attività di copia in Azure Data Factory

## <a name="overview"></a>Panoramica

> [!div class="op_single_selector" title1="Selezionare uSelezionare la versione del servizio di Azure Data Factory in uso:"]
> * [Versione 1](v1/data-factory-data-movement-activities.md)
> * [Versione corrente](copy-activity-overview.md)

In Azure Data Factory è possibile usare l'attività di copia per copiare i dati da archivi dati locali e cloud. Una volta copiati, i dati possono essere ulteriormente trasformati e analizzati tramite altre attività. L'attività di copia può essere usata anche per pubblicare risultati di trasformazione e analisi per Business Intelligence (BI) e l'uso delle applicazioni.

![Ruolo dell'attività di copia](media/copy-activity-overview/copy-activity.png)

L'attività di copia viene eseguita in un [runtime di integrazione](concepts-integration-runtime.md). Per diversi scenari di copia dei dati, è possibile sfruttare diverse versioni di Integration Runtime:

* Quando si copiano dati tra archivi dati accessibili pubblicamente tramite Internet da qualsiasi IP, l'attività di copia può essere abilitata da **Azure Integration Runtime**, che è sicura, affidabile, scalabile e [disponibile a livello globale](concepts-integration-runtime.md#integration-runtime-location).
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

L’attività di copia supporta anche la lettura e scrittura di file in formati specificati: **Testo, JSON, avro, ORC e parquet**e compressione e decompressione dei file con i codec seguenti: **Gzip, deflate, bzip2 e ZipDeflate**. Vedere [Formati di compressione e file supportati](supported-file-formats-and-compression-codecs.md) per i dettagli.

È possibile ad esempio eseguire queste attività di copia:

* Copiare i dati in SQL Server locali e scrivere in Azure Data Lake Storage Gen2 in formato parquet.
* Copiare i file in formato testo (CSV) dal file system locale e scrivere nel BLOB di Azure nel formato Avro.
* Copiare i file compressi dal file system locale e decomprimerli Azure Data Lake Storage Gen2.
* Copiare i dati in formato testo (CSV) con compressione GZip dal BLOB di Azure e scrivere nel database SQL di Azure.
* E molti altri casi con necessità di serializzazione/deserializzazione o di compressione/decompressione.

## <a name="supported-regions"></a>Aree supportate

Tuttavia, il servizio alla base dell'attività di copia è disponibile a livello globale nelle aree geografiche riportate nella sezione [Località del runtime di integrazione](concepts-integration-runtime.md#integration-runtime-location). La topologia disponibile a livello globale garantisce uno spostamento di dati efficiente e di solito consente di evitare passaggi tra diverse aree. Per la disponibilità del servizio Data Factory e lo spostamento dei dati in un'area, vedere [Servizi in base all'area](https://azure.microsoft.com/regions/#services) .

## <a name="configuration"></a>Configurazione

Per usare l'attività di copia in Azure Data Factory, è necessario:

1. **Creare servizi collegati per gli archivi dati di origine e sink.** Per configurare le proprietà supportate, fare riferimento alla sezione "Proprietà del servizio collegato" dell'articolo sul connettore. È possibile trovare un elenco dei connettori supportati nella sezione relativa agli [archivi di dati e ai formati supportati](#supported-data-stores-and-formats).
2. **Creare i set di dati per origine e sink.** Vedere la sezione "Proprietà set di dati" degli articoli del connettore di origine e sink per la configurazione e le proprietà supportate.
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
            "dataIntegrationUnits": <number>,
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

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type di un'attività di copia deve essere impostata su: **Copia** | Sì |
| inputs | Specificare il set di dati creato che fa riferimento ai dati di origine. L'attività di copia supporta un singolo input. | Sì |
| outputs | Specificare il set di dati creato che fa riferimento ai dati sink. L'attività di copia supporta un singolo output. | Sì |
| typeProperties | Un gruppo di proprietà per configurare l'attività di copia. | Sì |
| source | Specificare il tipo di origine della copia e le proprietà corrispondenti per recuperare i dati.<br/><br/>Vedere la sezione "Proprietà dell'attività di copia" nell'articolo sul connettore elencato in [Archivi dati e formati supportati](#supported-data-stores-and-formats). | Sì |
| sink | Specificare il tipo di sink della copia e le proprietà corrispondenti per scrivere i dati.<br/><br/>Vedere la sezione "Proprietà dell'attività di copia" nell'articolo sul connettore elencato in [Archivi dati e formati supportati](#supported-data-stores-and-formats). | Sì |
| translator | Specificare il mapping esplicito di colonne da origine a sink. Si applica quando il comportamento di copia predefinito non soddisfa le esigenze.<br/><br/>Vedere [Mapping dello schema e dei tipi di dati](copy-activity-schema-and-type-mapping.md). | No |
| dataIntegrationUnits | Specificare l'efficacia del [runtime di integrazione di Azure](concepts-integration-runtime.md) per migliorare la copia dei dati. Precedentemente noto come Unità di spostamento dati cloud. <br/><br/>Altre informazioni disponibili in [Data Integration Units](copy-activity-performance.md#data-integration-units) (Unità di integrazione dei dati). | No |
| parallelCopies | Specificare il parallelismo che l'attività di copia deve usare durante la lettura dei dati dall'origine e la scrittura dei dati nel sink.<br/><br/>Vedere [Copia parallela](copy-activity-performance.md#parallel-copy). | No |
| enableStaging<br/>stagingSettings | Scegliere di eseguire il staging dei dati provvisori in un archivio BLOB anziché copiare direttamente i dati dall'origine al sink.<br/><br/>Informazioni sugli scenari e sui dettagli di configurazione in [Copia di staging](copy-activity-performance.md#staged-copy). | No |
| enableSkipIncompatibleRow<br/>redirectIncompatibleRowSettings| Scegliere come gestire le righe incompatibili durante la copia di dati dall'origine al sink.<br/><br/>Vedere [Tolleranza di errore](copy-activity-fault-tolerance.md). | No |

## <a name="monitoring"></a>Monitoraggio

È possibile monitorare l'esecuzione dell'attività di copia nell'interfaccia utente "Crea e monitora" di Azure Data Factory o a livello di codice.

### <a name="monitor-visually"></a>Monitorare visivamente

Per monitorare visivamente l'esecuzione dell'attività di copia, selezionare -> **Copia e monitora** -> **scheda Monitoraggio** della data factory. Verrà visualizzato un elenco di esecuzioni di pipeline con un collegamento per la visualizzazione delle esecuzioni delle attività nella colonna **Azioni**.

![Monitorare le esecuzioni di pipeline](./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png)

Fare clic per visualizzare l'elenco delle attività in questa esecuzione di pipeline. Nella colonna **Azioni** sono presenti collegamenti all'input e all'output dell'attività di copia, agli errori (se l'esecuzione dell'attività di copia ha esito negativo) e ai dettagli.

![Monitorare le esecuzioni delle attività](./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png)

Fare clic sul collegamento "**Dettagli**" in **Azioni** per visualizzare i dettagli di esecuzione e le caratteristiche delle prestazioni dell'attività di copia. Vengono visualizzate informazioni quali il volume, le righe e i file di dati copiati dall'origine al sink, la velocità effettiva, i passaggi eseguiti (con la durata corrispondente) e le configurazioni usate per lo scenario di copia.

>[!TIP]
>Per alcuni scenari, si vedrà anche "suggerimenti per l'**ottimizzazione delle prestazioni**" nella parte superiore della pagina di monitoraggio della copia, che indica il collo di bottiglia identificato e guida gli elementi da modificare in modo da aumentare la velocità effettiva di copia, vedere un esempio con i dettagli [qui](#performance-and-tuning).

**Esempio: copia da Amazon S3 ad Azure Data Lake Store**
![Dettagli esecuzione attività di monitoraggio](./media/copy-activity-overview/monitor-activity-run-details-adls.png)

**Esempio: copia dal database SQL di Azure ad Azure SQL Data Warehouse tramite una copia di gestione temporanea**
![Dettagli esecuzione attività di monitoraggio](./media/copy-activity-overview/monitor-activity-run-details-sql-dw.png)

### <a name="monitor-programmatically"></a>Monitorare a livello di codice

I dettagli dell'esecuzione dell'attività di copia e le caratteristiche delle prestazioni vengono restituiti anche nella sezione risultato dell'esecuzione dell'attività di copia > output. Di seguito viene offerto l'elenco completo, ma solo i dettagli applicabili al proprio scenario di copia vengono visualizzati. Informazioni su come monitorare l'esecuzione dell'attività nella [sezione sull'avvio rapido del monitoraggio](quickstart-create-data-factory-dot-net.md#monitor-a-pipeline-run).

| Nome proprietà  | DESCRIZIONE | Unità |
|:--- |:--- |:--- |
| dataRead | Dimensioni dei dati letti nell'origine | Valore Int64 in **byte** |
| dataWritten | Dimensioni dei dati scritti nel sink | Valore Int64 in **byte** |
| filesRead | Numero di file copiati quando si copiano dati da una risorsa di archiviazione di file. | Valore Int64 (senza unità) |
| filesWritten | Numero di file copiati quando si copiano dati in una risorsa di archiviazione di file. | Valore Int64 (senza unità) |
| sourcePeakConnections | Numero massimo di connessioni simultanee stabilite nell'archivio dati di origine durante l'esecuzione dell'attività di copia. | Valore Int64 (senza unità) |
| sinkPeakConnections | Numero massimo di connessioni simultanee stabilite per l'archivio dati sink durante l'esecuzione dell'attività di copia. | Valore Int64 (senza unità) |
| rowsRead | Numero di righe lette dall'origine (non applicabili per la copia binaria). | Valore Int64 (senza unità) |
| rowsCopied | Numero di righe copiate nel sink (non applicabile per la copia binaria). | Valore Int64 (senza unità) |
| rowsSkipped | Numero di righe incompatibili ignorate. È possibile abilitare la funzionalità impostando il valore "enableSkipIncompatibleRow" su True. | Valore Int64 (senza unità) |
| copyDuration | Durata della copia. | Valore Int32 in secondi |
| throughput | Rapporto con il quale vengono trasferiti i dati. | Numero a virgola mobile in **KB/s** |
| sourcePeakConnections | Numero massimo di connessioni simultanee stabilite nell'archivio dati di origine durante la copia. | Valore Int32 |
| sinkPeakConnections| Numero massimo di connessioni simultanee stabilite nell'archivio dati sink durante la copia.| Valore Int32 |
| sqlDwPolyBase | Se PolyBase viene usato per copiare i dati in SQL Data Warehouse. | Boolean |
| redshiftUnload | Se UNLOAD viene usato per copiare i dati da Redshift. | Boolean |
| hdfsDistcp | Se DistCp viene usato per copiare i dati da HDFS. | Boolean |
| effectiveIntegrationRuntime | Mostra gli Integration Runtime usati per migliorare l'esecuzione dell'attività, nel formato `<IR name> (<region if it's Azure IR>)`. | Testo (stringa) |
| usedDataIntegrationUnits | Le unità di integrazione dati effettive durante la copia. | Valore Int32 |
| usedParallelCopies | Proprietà parallelCopies effettiva durante la copia. | Valore Int32 |
| redirectRowPath | Percorso del log delle righe incompatibili ignorate nel contenitore di archiviazione BLOB configurato in "redirectIncompatibleRowSettings". Vedere l'esempio seguente. | Testo (stringa) |
| executionDetails | Maggiori dettagli sulle fasi a cui viene sottoposta l'attività di copia e sui passaggi, sulla durata, sulle configurazioni usate e così via. Non è consigliabile analizzare questa sezione, perché può cambiare.<br/><br/>ADF riporta anche le durate dettagliate (in secondi) dedicate ai rispettivi `detailedDurations`passaggi in. Le durate di questi passaggi sono esclusive e vengono visualizzate solo quelle che si applicano all'esecuzione dell'attività di copia specificata:<br/>- **Durata** dell'Accodamento (`queuingDuration`): Tempo trascorso fino a quando l'attività di copia non inizia effettivamente sul runtime di integrazione. Se si usa il runtime di integrazione self-hosted e questo valore è di grandi dimensioni, suggerire di controllare la capacità e l'utilizzo di IR e aumentare o ridurre le prestazioni in base al carico di lavoro. <br/>- **Durata dello script di pre-copia** (`preCopyScriptDuration`): Tempo trascorso tra l'attività di copia a partire da IR e l'attività di copia che termina l'esecuzione dello script di pre-copia nell'archivio dati sink. Applicare quando si configura lo script di pre-copia. <br/>- **Time-to-first-byte** (`timeToFirstByte`): Tempo trascorso tra la fine del passaggio precedente e l'IR che riceve il primo byte dall'archivio dati di origine. Si applicano a un'origine non basata su file. Se questo valore è grande, suggerire di controllare e ottimizzare la query o il server.<br/>- **Durata trasferimento** (`transferDuration`): Il tempo trascorso tra la fine del passaggio precedente e l'IR che trasferisce tutti i dati dall'origine al sink. | Array |
| perfRecommendation | Copia suggerimenti per l'ottimizzazione delle prestazioni. Per informazioni dettagliate, vedere la sezione [prestazioni e ottimizzazione](#performance-and-tuning) . | Array |

```json
"output": {
    "dataRead": 6198358,
    "dataWritten": 19169324,
    "filesRead": 1,
    "sourcePeakConnections": 1,
    "sinkPeakConnections": 2,
    "rowsRead": 39614,
    "rowsCopied": 39614,
    "copyDuration": 1325,
    "throughput": 4.568,
    "errors": [],
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (West US)",
    "usedDataIntegrationUnits": 4,
    "usedParallelCopies": 1,
    "executionDetails": [
        {
            "source": {
                "type": "AzureBlobStorage"
            },
            "sink": {
                "type": "AzureSqlDatabase"
            },
            "status": "Succeeded",
            "start": "2019-08-06T01:01:36.7778286Z",
            "duration": 1325,
            "usedDataIntegrationUnits": 4,
            "usedParallelCopies": 1,
            "detailedDurations": {
                "queuingDuration": 2,
                "preCopyScriptDuration": 12,
                "transferDuration": 1311
            }
        }
    ],
    "perfRecommendation": [
        {
            "Tip": "Sink Azure SQL Database: The DTU utilization was high during the copy activity run. To achieve better performance, you are suggested to scale the database to a higher tier than the current 1600 DTUs.",
            "ReferUrl": "https://go.microsoft.com/fwlink/?linkid=2043368",
            "RuleName": "AzureDBTierUpgradePerfRecommendRule"
        }
    ]
}
```

## <a name="schema-and-data-type-mapping"></a>Mapping dello schema e dei tipi di dati

Vedere [Mapping dello schema e dei tipi di dati](copy-activity-schema-and-type-mapping.md) per informazioni su come l'attività di copia esegue il mapping dei dati di origine al sink.

## <a name="fault-tolerance"></a>Tolleranza di errore

Per impostazione predefinita, l'attività di copia interrompe la copia dei dati e restituisce un errore quando rileva dati incompatibili tra l'origine e il sink. È possibile applicare una configurazione che preveda di ignorare e registrare le righe incompatibili e di copiare soltanto i dati compatibili affinché la copia vada a buon fine. Per altri dettagli, vedere la [Tolleranza di errore dell'attività di copia: ignorare le righe incompatibili](copy-activity-fault-tolerance.md).

## <a name="performance-and-tuning"></a>Prestazioni e ottimizzazione

Vedere l'articolo [Guida alle prestazioni delle attività di copia e all'ottimizzazione](copy-activity-performance.md), che descrive i fattori chiave che influiscono sulle prestazioni di spostamento dei dati (attività di copia) in Data Factory di Azure. Vengono anche elencate le prestazioni osservate durante il test interni e vengono descritti i modi per ottimizzare le prestazioni dell'attività di copia.

In alcuni casi, quando si esegue un'attività di copia in Azure Data Factory, viene direttamente visualizzato "**Performance tuning tips**" (Suggerimenti per l'ottimizzazione delle prestazioni) nella parte superiore della [pagina di monitoraggio dell'attività di copia](#monitor-visually), come illustrato nell'esempio seguente. Il testo nel riquadro non solo indica un collo di bottiglia identificato per l'esecuzione della copia specificata, ma descrive anche gli elementi da modificare in modo da migliorare la velocità effettiva di copia. I suggerimenti per l'ottimizzazione delle prestazioni forniscono indicazioni utili, come ad esempio usare PolyBase per la copia dei dati in Azure SQL Data Warehouse, aumentare le unità UR di Azure Cosmos DB o DTU del database SQL di Azure se il collo di bottiglia dipende dalla risorsa nell'archivio dati, rimuovere la copia di gestione temporanea non necessaria e così via. Anche le regole di ottimizzazione delle prestazioni verranno gradualmente migliorate.

**Esempio: copia nel database SQL di Azure con i suggerimenti per l'ottimizzazione delle prestazioni**

In questo esempio, durante l'esecuzione della copia, ADF rileva che il database SQL di Azure sink raggiunge un utilizzo elevato di DTU che rallenta le operazioni di scrittura, quindi il suggerimento è quello di aumentare il livello del database SQL di Azure con più DTU.

![Monitoraggio della copia con suggerimenti per l'ottimizzazione delle prestazioni](./media/copy-activity-overview/copy-monitoring-with-performance-tuning-tips.png)

## <a name="incremental-copy"></a>Copia incrementale
Data Factory supporta scenari per la copia incrementale dei dati Delta da un archivio dati di origine a un archivio dati sink. Vedere [Esercitazione: Copiare dati in modo incrementale](tutorial-incremental-copy-overview.md).

## <a name="read-and-write-partitioned-data"></a>Leggere e scrivere dati partizionati
Nella versione 1, Azure Data Factory supportava la lettura o la scrittura di dati partizionati usando le variabili di sistema SliceStart/SliceEnd/WindowStart/WindowEnd. Nella versione corrente è possibile ottenere questo comportamento usando un parametro della pipeline e l'ora di inizio/ora prevista per il trigger come valore per il parametro. Per altre informazioni, vedere [Come leggere o scrivere dati partizionati](how-to-read-write-partitioned-data.md).

## <a name="next-steps"></a>Passaggi successivi
Vedere le guide rapide, le esercitazioni e gli esempi seguenti:

- [Copiare i dati da una posizione a un'altra nella stessa archiviazione BLOB di Azure](quickstart-create-data-factory-dot-net.md)
- [Copiare i dati dall'archiviazione BLOB di Azure al database SQL di Azure](tutorial-copy-data-dot-net.md)
- [Copiare i dati da un'istanza di SQL Server locale ad Azure](tutorial-hybrid-copy-powershell.md)

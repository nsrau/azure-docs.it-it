---
title: Attività di copia in Azure Data Factory
description: Informazioni sull'attività di copia in Azure Data Factory. È possibile usarlo per copiare dati da un archivio dati di origine supportato in un archivio dati sink supportato.
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
ms.openlocfilehash: b88983b4941143e5323ee795908cb332bdd79817
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73678407"
---
# <a name="copy-activity-in-azure-data-factory"></a>Attività di copia in Azure Data Factory

> [!div class="op_single_selector" title1="Selezionare la versione di Data Factory che si sta usando:"]
> * [Versione 1](v1/data-factory-data-movement-activities.md)
> * [Versione corrente](copy-activity-overview.md)

In Azure Data Factory, è possibile usare l'attività di copia per copiare i dati tra archivi dati locali e nel cloud. Dopo aver copiato i dati, è possibile usare altre attività per trasformarli e analizzarli. È anche possibile usare l'attività di copia per pubblicare i risultati di trasformazione e analisi per business intelligence (BI) e l'utilizzo di applicazioni.

![Ruolo dell'attività di copia](media/copy-activity-overview/copy-activity.png)

L'attività di copia viene eseguita in un [runtime di integrazione](concepts-integration-runtime.md). È possibile usare diversi tipi di runtime di integrazione per diversi scenari di copia dei dati:

* Quando si copiano dati tra due archivi dati accessibili pubblicamente tramite Internet da qualsiasi IP, è possibile usare il runtime di integrazione di Azure per l'attività di copia. Questo runtime di integrazione è sicuro, affidabile, scalabile e [disponibile a livello globale](concepts-integration-runtime.md#integration-runtime-location).
* Quando si copiano dati da e verso archivi dati che si trovano in locale o in una rete con controllo di accesso (ad esempio, una rete virtuale di Azure), è necessario configurare un runtime di integrazione self-hosted.

Un runtime di integrazione deve essere associato a ogni archivio dati di origine e sink. Per informazioni sul modo in cui l'attività di copia determina il runtime di integrazione da usare, vedere [determinazione dell'IR da usare](concepts-integration-runtime.md#determining-which-ir-to-use).

Per copiare dati da un'origine a un sink, il servizio che esegue l'attività di copia esegue i passaggi seguenti:

1. Legge i dati dall'archivio dati di origine.
2. Esegue la serializzazione/deserializzazione, la compressione/decompressione, il mapping di colonne e così via. Questa operazione viene eseguita in base alla configurazione del set di dati di input, del set di dati di output e dell'attività di copia.
3. Scrive i dati nell'archivio dati sink/di destinazione.

![Panoramica dell'attività di copia](media/copy-activity-overview/copy-activity-overview.png)

## <a name="supported-data-stores-and-formats"></a>Archivi dati e formati supportati

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

### <a name="supported-file-formats"></a>Formati di file supportati

È possibile usare l'attività di copia per copiare i file così come sono tra due archivi dati basati su file. In questo caso, i dati vengono copiati in modo efficiente senza serializzazione o deserializzazione.

L'attività di copia può inoltre leggere e scrivere nei file nei formati seguenti:
- Text
- JSON
- Avro
- ORC
- PARQUET

L'attività di copia può comprimere e decomprimere i file con questi codec: 
- Gzip
- Deflate
- Bzip2
- ZipDeflate

Per ulteriori informazioni, vedere [formati di compressione e file supportati](supported-file-formats-and-compression-codecs.md).

È ad esempio possibile eseguire le attività di copia seguenti:

* Copiare i dati da un database di SQL Server locale e scrivere i dati in Azure Data Lake Storage Gen2 in formato parquet.
* Copiare i file in formato testo (CSV) da un file system locale e scrivere nell'archiviazione BLOB di Azure nel formato avro.
* Copiare i file compressi da un file system locale, decomprimerli e scriverli nel Azure Data Lake Storage Gen2.
* Copiare i dati nel formato di testo compresso gzip dall'archiviazione BLOB di Azure e scriverli nel database SQL di Azure.
* Molte altre attività che richiedono la serializzazione/deserializzazione o la compressione/decompressione.

## <a name="supported-regions"></a>Aree supportate

Il servizio che Abilita l'attività di copia è disponibile a livello globale nelle aree geografiche elencate in [percorsi di runtime di integrazione di Azure](concepts-integration-runtime.md#integration-runtime-location). La topologia disponibile a livello globale garantisce uno spostamento di dati efficiente e di solito consente di evitare passaggi tra diverse aree. Vedere i [prodotti in base all'area](https://azure.microsoft.com/regions/#services) per verificare la disponibilità di data factory e lo spostamento dei dati in un'area specifica.

## <a name="configuration"></a>Configurazione

Per usare l'attività di copia in Azure Data Factory, è necessario:

1. **Creare servizi collegati per l'archivio dati di origine e l'archivio dati sink.** Per informazioni sulla configurazione e sulle proprietà supportate, vedere la sezione "proprietà del servizio collegato" dell'articolo del connettore. È possibile trovare l'elenco dei connettori supportati nella sezione [archivi dati e formati supportati](#supported-data-stores-and-formats) di questo articolo.
2. **Creare set di dati per l'origine e il sink.** Per informazioni sulla configurazione e sulle proprietà supportate, vedere le sezioni "proprietà dei set di dati" degli articoli sui connettori di origine e sink.
3. **Creare una pipeline con l'attività di copia.** Nella sezione seguente viene illustrato un esempio.

### <a name="syntax"></a>Sintassi

Il modello seguente di un'attività di copia contiene un elenco completo delle proprietà supportate. Specificare quelle più adatte per il proprio scenario.

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

#### <a name="syntax-details"></a>Dettagli sintassi

| Proprietà | Descrizione | Obbligatorio? |
|:--- |:--- |:--- |
| type | Per un'attività di copia, impostare su `Copy` | Sì |
| inputs | Specificare il set di dati creato che fa riferimento ai dati di origine. L'attività di copia supporta solo un singolo input. | Sì |
| outputs | Specificare il set di dati creato che punta ai dati del sink. L'attività di copia supporta solo un singolo output. | Sì |
| typeProperties | Specificare le proprietà per configurare l'attività di copia. | Sì |
| una sezione source | Specificare il tipo di origine della copia e le proprietà corrispondenti per il recupero dei dati.<br/><br/>Per ulteriori informazioni, vedere la sezione "proprietà dell'attività di copia" nell'articolo del connettore elencato in [archivi dati e formati supportati](#supported-data-stores-and-formats). | Sì |
| sink | Specificare il tipo di sink di copia e le proprietà corrispondenti per la scrittura dei dati.<br/><br/>Per ulteriori informazioni, vedere la sezione "proprietà dell'attività di copia" nell'articolo del connettore elencato in [archivi dati e formati supportati](#supported-data-stores-and-formats). | Sì |
| translator | Specificare il mapping esplicito di colonne da origine a sink. Questa proprietà si applica quando il comportamento di copia predefinito non soddisfa le proprie esigenze.<br/><br/>Per ulteriori informazioni, vedere [mapping dello schema nell'attività di copia](copy-activity-schema-and-type-mapping.md). | No |
| dataIntegrationUnits | Specificare una misura che rappresenta la quantità di energia utilizzata dal [runtime di integrazione di Azure](concepts-integration-runtime.md) per la copia dei dati. Queste unità erano precedentemente note come unità di spostamento dati cloud (spostamento dati). <br/><br/>Per ulteriori informazioni, vedere [Data Integration Unit](copy-activity-performance.md#data-integration-units). | No |
| parallelCopies | Specificare il parallelismo che si desidera venga utilizzato dall'attività di copia durante la lettura dei dati dall'origine e la scrittura dei dati nel sink.<br/><br/>Per ulteriori informazioni, vedere [copia parallela](copy-activity-performance.md#parallel-copy). | No |
| enableStaging<br/>stagingSettings | Consente di specificare se organizzare temporaneamente i dati provvisori nell'archivio BLOB anziché copiare direttamente i dati dall'origine al sink.<br/><br/>Per informazioni sugli scenari e i dettagli di configurazione utili, vedere la pagina relativa alla [copia temporanea](copy-activity-performance.md#staged-copy). | No |
| enableSkipIncompatibleRow<br/>redirectIncompatibleRowSettings| Scegliere come gestire le righe incompatibili durante la copia dei dati dall'origine al sink.<br/><br/>Per altre informazioni, vedere [tolleranza di errore](copy-activity-fault-tolerance.md). | No |

## <a name="monitoring"></a>Monitoraggio

È possibile monitorare l'esecuzione dell'attività di copia in Azure Data Factory **autore & monitorare** l'interfaccia utente o a livello di codice.

### <a name="monitor-visually"></a>Monitorare visivamente

Per monitorare visivamente l'esecuzione dell'attività di copia, passare alla data factory, quindi passare a **crea & monitoraggio**. Nella scheda **monitoraggio** viene visualizzato un elenco di esecuzioni di pipeline con un pulsante **Visualizza esecuzione attività** nella colonna **azioni** :

![Monitorare le esecuzioni di pipeline](./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png)

Selezionare **Visualizza esecuzioni attività** per visualizzare l'elenco delle attività nell'esecuzione della pipeline. Nella colonna **azioni** vengono visualizzati i collegamenti all'input dell'attività di copia, l'output, gli errori (se l'esecuzione dell'attività di copia ha esito negativo) e i dettagli:

![Monitorare le esecuzioni delle attività](./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png)

Selezionare il pulsante **Dettagli** nella colonna **azioni** per visualizzare i dettagli sull'esecuzione dell'attività di copia e le caratteristiche delle prestazioni. Vengono visualizzate informazioni quali volume/numero di righe/numero di file di dati copiati dall'origine al sink, la velocità effettiva, i passaggi che l'attività di copia passa con le durate corrispondenti e le configurazioni usate per lo scenario di copia.

>[!TIP]
>In alcuni scenari verranno visualizzati anche i suggerimenti per l' **ottimizzazione delle prestazioni** nella parte superiore della pagina di monitoraggio della copia. Questi suggerimenti indicano i colli di bottiglia identificati e forniscono informazioni sugli elementi da modificare per aumentare la velocità effettiva della copia. Per un esempio, vedere la sezione [prestazioni e ottimizzazione](#performance-and-tuning) di questo articolo.

**Esempio: copiare da Amazon S3 a Azure Data Lake Store**
![monitorare i dettagli dell'esecuzione dell'attività](./media/copy-activity-overview/monitor-activity-run-details-adls.png)

**Esempio: copiare dal database SQL di Azure a Azure SQL data warehouse con la copia** di staging
i dettagli dell'esecuzione dell'attività ![monitor](./media/copy-activity-overview/monitor-activity-run-details-sql-dw.png)

### <a name="monitor-programmatically"></a>Monitorare a livello di codice

I dettagli dell'esecuzione dell'attività di copia e le caratteristiche delle prestazioni vengono restituiti anche nella sezione **Risultati esecuzione attività di copia** > **output** . Di seguito è riportato un elenco completo delle proprietà che possono essere restituite. Verranno visualizzate solo le proprietà applicabili allo scenario di copia. Per informazioni su come monitorare le esecuzioni delle attività, vedere [monitorare un'esecuzione della pipeline](quickstart-create-data-factory-dot-net.md#monitor-a-pipeline-run).

| Nome proprietà  | Descrizione | Unità |
|:--- |:--- |:--- |
| dataRead | Quantità di dati letti dall'origine. | Valore Int64, in byte |
| dataWritten | Quantità di dati scritti nel sink. | Valore Int64, in byte |
| filesRead | Numero di file copiati durante la copia dall'archiviazione file. | Valore Int64 (senza unità) |
| filesWritten | Numero di file copiati durante la copia nell'archiviazione file. | Valore Int64 (senza unità) |
| sourcePeakConnections | Numero massimo di connessioni simultanee stabilite nell'archivio dati di origine durante l'esecuzione dell'attività di copia. | Valore Int64 (senza unità) |
| sinkPeakConnections | Numero massimo di connessioni simultanee stabilite nell'archivio dati sink durante l'esecuzione dell'attività di copia. | Valore Int64 (senza unità) |
| rowsRead | Numero di righe lette dall'origine (non applicabile per la copia binaria). | Valore Int64 (senza unità) |
| rowsCopied | Numero di righe copiate nel sink (non applicabile per la copia binaria). | Valore Int64 (senza unità) |
| rowsSkipped | Numero di righe non compatibili ignorate. È possibile abilitare l'omissione di righe incompatibili impostando `enableSkipIncompatibleRow` su true. | Valore Int64 (senza unità) |
| copyDuration | Durata dell'esecuzione della copia. | Valore Int32, in secondi |
| throughput | Frequenza di trasferimento dei dati. | Numero a virgola mobile, in KBps |
| sourcePeakConnections | Numero massimo di connessioni simultanee stabilite nell'archivio dati di origine durante l'esecuzione dell'attività di copia. | Valore Int32 (nessuna unità) |
| sinkPeakConnections| Numero massimo di connessioni simultanee stabilite nell'archivio dati sink durante l'esecuzione dell'attività di copia.| Valore Int32 (nessuna unità) |
| sqlDwPolyBase | Indica se la polibase viene utilizzata quando i dati vengono copiati in SQL Data Warehouse. | Booleano |
| redshiftUnload | Indica se lo SCARICAmento viene utilizzato quando i dati vengono copiati da spostamento verso il suo | Booleano |
| hdfsDistcp | Indica se DistCp viene usato quando i dati vengono copiati da HDFS. | Booleano |
| effectiveIntegrationRuntime | Runtime di integrazione (IR) o Runtime usati per l'esecuzione dell'attività, nel formato `<IR name> (<region if it's Azure IR>)`. | Testo (stringa) |
| usedDataIntegrationUnits | Le unità di integrazione dati effettive durante la copia. | Valore Int32 |
| usedParallelCopies | Proprietà parallelCopies effettiva durante la copia. | Valore Int32 |
| redirectRowPath | Percorso del log delle righe non compatibili ignorate nell'archivio BLOB configurato nella proprietà `redirectIncompatibleRowSettings`. Vedere [tolleranza di errore](#fault-tolerance) più avanti in questo articolo. | Testo (stringa) |
| executionDetails | Ulteriori dettagli sulle fasi dell'attività di copia e sui passaggi, le durate, le configurazioni e così via corrispondenti. Non è consigliabile analizzare questa sezione perché potrebbe cambiare.<br/><br/>Data Factory segnala anche le durate (in secondi) dedicate alle varie fasi della `detailedDurations`. Le durate di questi passaggi sono esclusive. Vengono visualizzate solo le durate valide per l'esecuzione dell'attività di copia specificata:<br/>**Durata di Accodamento** (`queuingDuration`): quantità di tempo prima che l'attività di copia venga effettivamente avviata nel runtime di integrazione. Se si usa un runtime di integrazione self-hosted e questo valore è elevato, controllare la capacità e l'utilizzo di IR e aumentare o ridurre le prestazioni in base al carico di lavoro. <br/>**Durata dello script di pre-copia** (`preCopyScriptDuration`): tempo trascorso tra il momento in cui l'attività di copia inizia nell'IR e quando l'attività di copia termina l'esecuzione dello script di pre-copia nell'archivio dati sink. Si applica quando si configura lo script di pre-copia. <br/>**Tempo per il primo byte** (`timeToFirstByte`): tempo trascorso tra la fine del passaggio precedente e l'ora in cui il runtime di integrazione riceve il primo byte dall'archivio dati di origine. Si applica alle origini non basate su file. Se questo valore è elevato, controllare e ottimizzare la query o il server.<br/>**Durata trasferimento** (`transferDuration`): tempo trascorso tra la fine del passaggio precedente e l'ora in cui il runtime di integrazione trasferisce tutti i dati dall'origine al sink. | Array |
| perfRecommendation | Copia suggerimenti per l'ottimizzazione delle prestazioni. Per informazioni dettagliate, vedere [prestazioni e ottimizzazione](#performance-and-tuning) . | Array |

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

Per informazioni sul modo in cui l'attività di copia esegue il mapping dei dati di origine al sink, vedere [mapping tra schemi e tipi di dati](copy-activity-schema-and-type-mapping.md) .

## <a name="fault-tolerance"></a>Tolleranza di errore

Per impostazione predefinita, l'attività di copia interrompe la copia dei dati e restituisce un errore quando le righe di dati di origine non sono compatibili con le righe di dati del sink. Per fare in modo che la copia abbia esito positivo, è possibile configurare l'attività di copia in modo da ignorare e registrare le righe incompatibili e copiare solo i dati compatibili. Per informazioni dettagliate, vedere [tolleranza di errore dell'attività di copia](copy-activity-fault-tolerance.md) .

## <a name="performance-and-tuning"></a>Prestazioni e ottimizzazione

La [Guida alla scalabilità e alle prestazioni dell'attività di copia](copy-activity-performance.md) descrive i fattori chiave che influiscono sulle prestazioni dello spostamento dei dati tramite l'attività di copia in Azure Data Factory. Vengono inoltre elencati i valori delle prestazioni osservati durante i test e viene illustrato come ottimizzare le prestazioni dell'attività di copia.

In alcuni scenari, quando si esegue un'attività di copia in Data Factory, verranno visualizzati i suggerimenti per l' **ottimizzazione delle prestazioni** nella parte superiore della [pagina Monitoraggio attività di copia](#monitor-visually), come illustrato nell'esempio seguente. I suggerimenti indicano il collo di bottiglia identificato per l'esecuzione della copia specificata. Forniscono anche informazioni sugli elementi da modificare per aumentare la velocità effettiva della copia. I suggerimenti per l'ottimizzazione delle prestazioni offrono attualmente suggerimenti come l'uso di polibase quando si copiano dati in Azure SQL Data Warehouse, aumentando Azure Cosmos DB ur o il database SQL di Azure DTU quando la risorsa sul lato archivio dati è il collo di bottiglia e la rimozione copie di gestione temporanea non necessarie.

**Esempio: copiare nel database SQL di Azure con un suggerimento per l'ottimizzazione delle prestazioni**

In questo esempio, durante l'esecuzione di una copia, Data Factory tiene traccia di un elevato utilizzo di DTU nel database SQL di Azure sink. Questa condizione rallenta le operazioni di scrittura. Il suggerimento consiste nell'aumentare la DTU nel livello del database SQL di Azure:

![Monitoraggio della copia con suggerimenti per l'ottimizzazione delle prestazioni](./media/copy-activity-overview/copy-monitoring-with-performance-tuning-tips.png)

## <a name="incremental-copy"></a>Copia incrementale
Data Factory consente di copiare in modo incrementale i dati differenziali da un archivio dati di origine a un archivio dati sink. Per informazioni dettagliate, vedere [esercitazione: copiare i dati in modo incrementale](tutorial-incremental-copy-overview.md).

## <a name="next-steps"></a>Passaggi successivi
Vedere le guide rapide, le esercitazioni e gli esempi seguenti:

- [Copiare i dati da una posizione a un'altra nello stesso account di archiviazione BLOB di Azure](quickstart-create-data-factory-dot-net.md)
- [Copiare dati da un archivio BLOB di Azure al database SQL di Azure](tutorial-copy-data-dot-net.md)
- [Copiare dati da un database di SQL Server locale in Azure](tutorial-hybrid-copy-powershell.md)

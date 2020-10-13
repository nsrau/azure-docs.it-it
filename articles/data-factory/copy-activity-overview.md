---
title: Attività di copia in Azure Data Factory
description: Informazioni sull'attività di copia in Azure Data Factory. È possibile usarlo per copiare dati da un archivio dati di origine supportato in un archivio dati sink supportato.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/12/2020
ms.author: jingwang
ms.openlocfilehash: 5eade0ad48dcdd1f0c18ef6e65e498a7b9c79c15
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/12/2020
ms.locfileid: "91951682"
---
# <a name="copy-activity-in-azure-data-factory"></a>Attività di copia in Azure Data Factory

> [!div class="op_single_selector" title1="Selezionare la versione di Data Factory che si sta usando:"]
> * [Versione 1](v1/data-factory-data-movement-activities.md)
> * [Versione corrente](copy-activity-overview.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

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

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

È possibile usare l'attività di copia per copiare i file così come sono tra due archivi dati basati su file, nel qual caso i dati vengono copiati in modo efficiente senza serializzazione o deserializzazione. Inoltre, è possibile analizzare o generare file con un formato specifico, ad esempio, è possibile eseguire le operazioni seguenti:

* Copiare i dati da un database di SQL Server e scrivere in Azure Data Lake Storage Gen2 in formato parquet.
* Copiare i file in formato testo (CSV) da un file system locale e scrivere nell'archiviazione BLOB di Azure nel formato avro.
* Copiare i file compressi da un file system locale, decomprimerli in tempo reale e scrivere i file estratti in Azure Data Lake Storage Gen2.
* Copiare i dati nel formato di testo compresso gzip dall'archiviazione BLOB di Azure e scriverli nel database SQL di Azure.
* Molte altre attività che richiedono la serializzazione/deserializzazione o la compressione/decompressione.

## <a name="supported-regions"></a>Aree supportate

Il servizio che Abilita l'attività di copia è disponibile a livello globale nelle aree geografiche elencate in [percorsi di runtime di integrazione di Azure](concepts-integration-runtime.md#integration-runtime-location). La topologia disponibile a livello globale garantisce uno spostamento di dati efficiente e di solito consente di evitare passaggi tra diverse aree. Vedere i [prodotti in base all'area](https://azure.microsoft.com/regions/#services) per verificare la disponibilità di data factory e lo spostamento dei dati in un'area specifica.

## <a name="configuration"></a>Configurazione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

In generale, per usare l'attività di copia in Azure Data Factory, è necessario:

1. **Creare servizi collegati per l'archivio dati di origine e l'archivio dati sink.** È possibile trovare l'elenco dei connettori supportati nella sezione [archivi dati e formati supportati](#supported-data-stores-and-formats) di questo articolo. Per informazioni sulla configurazione e sulle proprietà supportate, vedere la sezione "proprietà del servizio collegato" dell'articolo del connettore. 
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

| Proprietà | Descrizione | Necessaria? |
|:--- |:--- |:--- |
| tipo | Per un'attività di copia, impostare su `Copy` | Sì |
| input | Specificare il set di dati creato che fa riferimento ai dati di origine. L'attività di copia supporta solo un singolo input. | Sì |
| outputs | Specificare il set di dati creato che punta ai dati del sink. L'attività di copia supporta solo un singolo output. | Sì |
| typeProperties | Specificare le proprietà per configurare l'attività di copia. | Sì |
| source | Specificare il tipo di origine della copia e le proprietà corrispondenti per il recupero dei dati.<br/>Per ulteriori informazioni, vedere la sezione "proprietà dell'attività di copia" nell'articolo del connettore elencato in [archivi dati e formati supportati](#supported-data-stores-and-formats). | Sì |
| sink | Specificare il tipo di sink di copia e le proprietà corrispondenti per la scrittura dei dati.<br/>Per ulteriori informazioni, vedere la sezione "proprietà dell'attività di copia" nell'articolo del connettore elencato in [archivi dati e formati supportati](#supported-data-stores-and-formats). | Sì |
| translator | Specificare il mapping esplicito di colonne da origine a sink. Questa proprietà si applica quando il comportamento di copia predefinito non soddisfa le proprie esigenze.<br/>Per ulteriori informazioni, vedere [mapping dello schema nell'attività di copia](copy-activity-schema-and-type-mapping.md). | No |
| dataIntegrationUnits | Specificare una misura che rappresenta la quantità di energia utilizzata dal [runtime di integrazione di Azure](concepts-integration-runtime.md) per la copia dei dati. Queste unità erano precedentemente note come unità di spostamento dati cloud (spostamento dati). <br/>Per ulteriori informazioni, vedere [Data Integration Unit](copy-activity-performance-features.md#data-integration-units). | No |
| parallelCopies | Specificare il parallelismo che si desidera venga utilizzato dall'attività di copia durante la lettura dei dati dall'origine e la scrittura dei dati nel sink.<br/>Per ulteriori informazioni, vedere [copia parallela](copy-activity-performance-features.md#parallel-copy). | No |
| preserve | Consente di specificare se mantenere i metadati/ACL durante la copia dei dati. <br/>Per altre informazioni, vedere [preservare i metadati](copy-activity-preserve-metadata.md). |No |
| enableStaging<br/>stagingSettings | Consente di specificare se organizzare temporaneamente i dati provvisori nell'archivio BLOB anziché copiare direttamente i dati dall'origine al sink.<br/>Per informazioni sugli scenari e i dettagli di configurazione utili, vedere la pagina relativa alla [copia temporanea](copy-activity-performance-features.md#staged-copy). | No |
| enableSkipIncompatibleRow<br/>redirectIncompatibleRowSettings| Scegliere come gestire le righe incompatibili durante la copia dei dati dall'origine al sink.<br/>Per altre informazioni, vedere [tolleranza di errore](copy-activity-fault-tolerance.md). | No |

## <a name="monitoring"></a>Monitoraggio

È possibile monitorare l'esecuzione dell'attività di copia nel Azure Data Factory sia visivamente che a livello di codice. Per informazioni dettagliate, vedere [monitorare l'attività di copia](copy-activity-monitoring.md).

## <a name="incremental-copy"></a>Copia incrementale

Data Factory consente di copiare in modo incrementale i dati differenziali da un archivio dati di origine a un archivio dati sink. Per informazioni dettagliate, vedere [esercitazione: copiare i dati in modo incrementale](tutorial-incremental-copy-overview.md).

## <a name="performance-and-tuning"></a>Prestazioni e ottimizzazione

L'esperienza di [monitoraggio dell'attività di copia](copy-activity-monitoring.md) Mostra le statistiche sulle prestazioni di copia per ogni esecuzione dell'attività. La [Guida alla scalabilità e alle prestazioni dell'attività di copia](copy-activity-performance.md) descrive i fattori chiave che influiscono sulle prestazioni dello spostamento dei dati tramite l'attività di copia in Azure Data Factory. Vengono inoltre elencati i valori delle prestazioni osservati durante i test e viene illustrato come ottimizzare le prestazioni dell'attività di copia.

## <a name="resume-from-last-failed-run"></a>Riprendi dall'ultima esecuzione non riuscita

L'attività di copia supporta la ripresa dall'ultima esecuzione non riuscita quando si copiano grandi dimensioni dei file così come sono con il formato binario tra archivi basati su file e si sceglie di mantenere la gerarchia di cartelle o file dall'origine al sink, ad esempio per migrare i dati da Amazon S3 a Azure Data Lake Storage Gen2. Si applica ai seguenti connettori basati su file: [Amazon S3](connector-amazon-simple-storage-service.md), [BLOB di Azure](connector-azure-blob-storage.md), [Azure Data Lake storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake storage Gen2](connector-azure-data-lake-storage.md), [archiviazione file di Azure](connector-azure-file-storage.md), [file System](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md)e [SFTP](connector-sftp.md).

È possibile utilizzare l'attività di copia Resume nei due modi seguenti:

- **Nuovo tentativo a livello di attività:** È possibile impostare il numero di tentativi per l'attività di copia. Durante l'esecuzione della pipeline, se l'esecuzione dell'attività di copia non riesce, il successivo tentativo automatico verrà avviato dal punto di errore dell'ultima versione di valutazione.
- **Riesegui da attività non riuscita:** Al termine dell'esecuzione della pipeline, è anche possibile attivare una riesecuzione dall'attività non riuscita nella visualizzazione monitoraggio dell'interfaccia utente di ADF o a livello di codice. Se l'attività non riuscita è un'attività di copia, la pipeline non verrà rieseguita solo da questa attività, ma riprenderà anche dal punto di errore dell'esecuzione precedente.

    ![Copia ripresa](media/copy-activity-overview/resume-copy.png)

Pochi punti da notare:

- Resume si verifica a livello di file. Se l'attività di copia ha esito negativo durante la copia di un file, nella prossima esecuzione il file specifico verrà copiato di nuovo.
- Per il corretto funzionamento del ripristino, non modificare le impostazioni dell'attività di copia tra le riesecuzioni.
- Quando si copiano dati da Amazon S3, BLOB di Azure, Azure Data Lake Storage Gen2 e Google Cloud Storage, l'attività di copia può riprendere da un numero arbitrario di file copiati. Mentre per il resto dei connettori basati su file come origine, l'attività di copia supporta il ripristino da un numero limitato di file, in genere nell'intervallo di decine di migliaia e varia a seconda della lunghezza dei percorsi dei file; i file oltre questo numero verranno copiati di nuovo durante le riesecuzioni.

Per altri scenari rispetto alla copia binaria del file, la riesecuzione dell'attività di copia inizia dall'inizio.

## <a name="preserve-metadata-along-with-data"></a>Mantieni metadati insieme ai dati

Durante la copia dei dati dall'origine al sink, in scenari come data Lake Migration, è anche possibile scegliere di mantenere i metadati e gli ACL insieme ai dati usando l'attività di copia. Per informazioni dettagliate, vedere [mantenere i metadati](copy-activity-preserve-metadata.md) .

## <a name="schema-and-data-type-mapping"></a>Mapping dello schema e dei tipi di dati

Per informazioni sul modo in cui l'attività di copia esegue il mapping dei dati di origine al sink, vedere [mapping tra schemi e tipi di dati](copy-activity-schema-and-type-mapping.md) .

## <a name="add-additional-columns-during-copy"></a>Aggiungi altre colonne durante la copia

Oltre a copiare i dati dall'archivio dati di origine al sink, è anche possibile configurare per aggiungere ulteriori colonne di dati da copiare insieme a sink. Ad esempio:

- Quando si esegue la copia da un'origine basata su file, archiviare il percorso del file relativo come colonna aggiuntiva per tracciare da quale file provengono i dati.
- Duplicare la colonna di origine specificata come altra colonna. 
- Aggiungere una colonna con l'espressione ADF, per associare le variabili di sistema ADF, ad esempio l'ID pipeline o il nome della pipeline, oppure archiviare altro valore dinamico dall'output dell'attività upstream.
- Aggiungere una colonna con valore statico per soddisfare le esigenze di utilizzo a valle.

È possibile trovare la configurazione seguente nella scheda origine dell'attività di copia. È anche possibile eseguire il mapping delle colonne aggiuntive nel [mapping dello schema](copy-activity-schema-and-type-mapping.md#schema-mapping) dell'attività di copia come di consueto usando i nomi di colonna definiti. 

![Aggiungere altre colonne nell'attività di copia](./media/copy-activity-overview/copy-activity-add-additional-columns.png)

>[!TIP]
>Questa funzionalità funziona con il modello di set di dati più recente. Se questa opzione non viene visualizzata nell'interfaccia utente, provare a creare un nuovo set di dati.

Per configurarlo a livello di codice, aggiungere la `additionalColumns` proprietà nell'origine dell'attività di copia:

| Proprietà | Descrizione | Obbligatoria |
| --- | --- | --- |
| additionalColumns | Aggiungere altre colonne di dati da copiare nel sink.<br><br>Ogni oggetto sotto la `additionalColumns` matrice rappresenta una colonna aggiuntiva. `name`Definisce il nome della colonna e `value` indica il valore dei dati di tale colonna.<br><br>I valori dei dati consentiti sono:<br>- **`$$FILEPATH`** -una variabile riservata indica di archiviare il percorso relativo dei file di origine nel percorso di cartella specificato nel set di dati. Applicare all'origine basata su file.<br>- **$ $Column: <source_column_name>** -un modello di variabile riservata indica di duplicare la colonna di origine specificata come altra colonna<br>- **Espressione**<br>- **Valore statico** | No |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyWithAdditionalColumns",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "<source type>",
                "additionalColumns": [
                    {
                        "name": "filePath",
                        "value": "$$FILEPATH"
                    },
                    {
                        "name": "newColName",
                        "value": "$$COLUMN:SourceColumnA"
                    },
                    {
                        "name": "pipelineName",
                        "value": {
                            "value": "@pipeline().Pipeline",
                            "type": "Expression"
                        }
                    },
                    {
                        "name": "staticValue",
                        "value": "sampleValue"
                    }
                ],
                ...
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="auto-create-sink-tables"></a>Creazione automatica di tabelle di sink

Quando si copiano dati in un database SQL/Azure sinapsi Analytics, se la tabella di destinazione non esiste, l'attività di copia supporta la creazione automatica in base ai dati di origine. Mira a aiutarti a iniziare rapidamente a caricare i dati e a valutare il database SQL o l'analisi delle sinapsi di Azure. Dopo l'inserimento dei dati, è possibile esaminare e modificare lo schema della tabella di sink in base alle esigenze.

Questa funzionalità è supportata per la copia di dati da qualsiasi origine nei seguenti archivi dati sink. È possibile trovare l'opzione nell' *interfaccia utente di authoring di ADF* -> opzione *sink attività di copia* -> *tabella* -> *creazione automatica tabella*o `tableOption` Proprietà via nel payload del sink dell'attività di copia.

- [Database SQL di Azure](connector-azure-sql-database.md)
- [Istanza gestita di database SQL di Azure](connector-azure-sql-managed-instance.md)
- [Azure Synapse Analytics (in precedenza SQL Data Warehouse)](connector-azure-sql-data-warehouse.md)
- [SQL Server](connector-sql-server.md)

![Creazione di tabelle di sink](media/copy-activity-overview/create-sink-table.png)

## <a name="fault-tolerance"></a>Tolleranza di errore

Per impostazione predefinita, l'attività di copia interrompe la copia dei dati e restituisce un errore quando le righe di dati di origine non sono compatibili con le righe di dati del sink. Per fare in modo che la copia abbia esito positivo, è possibile configurare l'attività di copia in modo da ignorare e registrare le righe incompatibili e copiare solo i dati compatibili. Per informazioni dettagliate, vedere [tolleranza di errore dell'attività di copia](copy-activity-fault-tolerance.md) .

## <a name="next-steps"></a>Passaggi successivi
Vedere le guide rapide, le esercitazioni e gli esempi seguenti:

- [Copiare i dati da una posizione a un'altra nello stesso account di archiviazione BLOB di Azure](quickstart-create-data-factory-dot-net.md)
- [Copiare dati da un archivio BLOB di Azure al database SQL di Azure](tutorial-copy-data-dot-net.md)
- [Copiare dati da un database di SQL Server in Azure](tutorial-hybrid-copy-powershell.md)

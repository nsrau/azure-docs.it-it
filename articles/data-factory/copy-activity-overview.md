---
title: Attività di copia in Azure Data FactoryCopy activity in Azure Data Factory
description: Informazioni sull'attività Copia in Azure Data Factory.Learn about the Copy activity in Azure Data Factory. È possibile utilizzarlo per copiare i dati da un archivio dati di origine supportato a un archivio dati sink supportato.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: jingwang
ms.openlocfilehash: edb80c604951a140d21e3775eec3f1dc6d55af73
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421404"
---
# <a name="copy-activity-in-azure-data-factory"></a>Attività di copia in Azure Data FactoryCopy activity in Azure Data Factory

> [!div class="op_single_selector" title1="Selezionare la versione di Data Factory in uso:"]
> * [Versione 1](v1/data-factory-data-movement-activities.md)
> * [Versione corrente](copy-activity-overview.md)

In Azure Data Factory è possibile usare l'attività Copia per copiare i dati tra archivi dati situati in locale e nel cloud. Dopo aver copiato i dati, è possibile utilizzare altre attività per trasformarla e analizzarle ulteriormente. È inoltre possibile utilizzare l'attività Copia per pubblicare i risultati di trasformazione e analisi per la business intelligence (BI) e l'utilizzo delle applicazioni.

![Il ruolo dell'attività Copia](media/copy-activity-overview/copy-activity.png)

L'attività Copia viene eseguita in un runtime di [integrazione.](concepts-integration-runtime.md) È possibile utilizzare diversi tipi di runtime di integrazione per diversi scenari di copia dei dati:You can use different types of integration runtimes for different data copy scenarios:

* Quando si copiano dati tra due archivi dati accessibili pubblicamente tramite Internet da qualsiasi IP, è possibile usare il runtime di integrazione di Azure per l'attività di copia. Questo runtime di integrazione è sicuro, affidabile, scalabile e [disponibile a livello globale.](concepts-integration-runtime.md#integration-runtime-location)
* Quando si copiano dati da e verso archivi dati situati in locale o in una rete con controllo di accesso (ad esempio, una rete virtuale di Azure), è necessario configurare un runtime di integrazione self-hosted.

Un runtime di integrazione deve essere associato a ogni archivio dati di origine e sink. Per informazioni su come l'attività Copia determina il runtime di integrazione da utilizzare, vedere Determinazione del runtime di integrazione [da utilizzare.](concepts-integration-runtime.md#determining-which-ir-to-use)

Per copiare i dati da un'origine a un sink, il servizio che esegue l'attività di copia esegue i passaggi seguenti:To copy data from a source to a sink, the service that runs the Copy activity performs these steps:

1. Legge i dati dall'archivio dati di origine.
2. Esegue la serializzazione/deserializzazione, la compressione/decompressione, il mapping delle colonne e così via. Queste operazioni vengono eseguite in base alla configurazione del set di dati di input, del set di dati di output e dell'attività di copia.
3. Scrive i dati nell'archivio dati sink/di destinazione.

![Panoramica dell'attività di copia](media/copy-activity-overview/copy-activity-overview.png)

## <a name="supported-data-stores-and-formats"></a>Archivi dati e formati supportati

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

### <a name="supported-file-formats"></a>Formati di file supportati

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

È possibile utilizzare l'attività Copy per copiare i file così come sono tra due archivi dati basati su file, nel qual caso i dati vengono copiati in modo efficiente senza alcuna serializzazione o deserializzazione. Inoltre, è anche possibile analizzare o generare file di un determinato formato, ad esempio, è possibile eseguire le operazioni seguenti:In addition, you can also parse or generate files of a given format, for example, you can perform the following:

* Copiare i dati da un database di SQL Server locale e scrivere in Azure Data Lake Storage Gen2 in formato Parquet.Copy data from an on-premises SQL Server database and write to Azure Data Lake Storage Gen2 in Parquet format.
* Copiare i file in formato testo (CSV) da un file system locale e scrivere nell'archivio BLOB di Azure in formato Avro.Copy files in text (CSV) format from an on-premises file system and write to Azure Blob storage in Avro format.
* Copiare i file compressi da un file system locale, decomprimerli immediatamente e scrivere i file estratti in Azure Data Lake Storage Gen2.
* Copiare i dati in formato Gzip con testo compresso (CSV) dall'archivio BLOB di Azure e scriverlo nel database SQL di Azure.Copy data in Gzip compressed-text (CSV) format from Azure Blob storage and write it to Azure SQL Database.
* Molte altre attività che richiedono serializzazione/deserializzazione o compressione/decompressione.

## <a name="supported-regions"></a>Aree supportate

Il servizio che abilita l'attività Copia è disponibile a livello globale nelle aree e nelle aree geografiche elencate nei percorsi di [runtime dell'integrazione](concepts-integration-runtime.md#integration-runtime-location)di Azure. La topologia disponibile a livello globale garantisce uno spostamento di dati efficiente e di solito consente di evitare passaggi tra diverse aree. Vedere [Prodotti per area](https://azure.microsoft.com/regions/#services) per verificare la disponibilità di Data Factory e lo spostamento dei dati in un'area specifica.

## <a name="configuration"></a>Configurazione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

In generale, per usare l'attività Copia in Azure Data Factory, è necessario:In general, to use the Copy activity in Azure Data Factory, you need to:

1. **Creare servizi collegati per l'archivio dati di origine e l'archivio dati sink.** L'elenco dei connettori supportati è riportato nella sezione [Archivi dati e formati supportati](#supported-data-stores-and-formats) di questo articolo. Fare riferimento alla sezione "Proprietà del servizio collegato" dell'articolo del connettore per informazioni di configurazione e proprietà supportate. 
2. **Creare set di dati per l'origine e il sink.** Fare riferimento alle sezioni "Proprietà set di dati" degli articoli sui connettori di origine e sink per informazioni sulla configurazione e sulle proprietà supportate.
3. **Creare una pipeline con l'attività Copia.Create a pipeline with the Copy activity.** Nella sezione seguente viene illustrato un esempio.

### <a name="syntax"></a>Sintassi

Il modello seguente di un'attività Copy contiene un elenco completo delle proprietà supportate. Specificare quelle più adatte per il proprio scenario.

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
| type | Per un'attività Copia, impostare su`Copy` | Sì |
| inputs | Specificare il set di dati creato che punta ai dati di origine. L'attività Copy supporta un solo input. | Sì |
| outputs | Specificare il set di dati creato che punta ai dati sink. L'attività di copia supporta un solo output. | Sì |
| typeProperties | Specificare le proprietà per configurare l'attività Copia. | Sì |
| source | Specificare il tipo di origine di copia e le proprietà corrispondenti per il recupero dei dati.<br/>Per ulteriori informazioni, vedere la sezione "Proprietà dell'attività di copia" nell'articolo sul connettore elencato in [Formati e archivi dati supportati](#supported-data-stores-and-formats). | Sì |
| sink | Specificare il tipo di sink di copia e le proprietà corrispondenti per la scrittura dei dati.<br/>Per ulteriori informazioni, vedere la sezione "Proprietà dell'attività di copia" nell'articolo sul connettore elencato in [Formati e archivi dati supportati](#supported-data-stores-and-formats). | Sì |
| translator | Specificare il mapping esplicito di colonne da origine a sink. Questa proprietà si applica quando il comportamento di copia predefinito non soddisfa le proprie esigenze.<br/>Per ulteriori informazioni, vedere [Mapping dello schema nell'attività](copy-activity-schema-and-type-mapping.md)di copia . | No |
| dataIntegrationUnits | Specificare una misura che rappresenta la quantità di potenza usata dal runtime di [integrazione](concepts-integration-runtime.md) di Azure per la copia dei dati. Queste unità erano precedentemente note come unità di spostamento dei dati cloud (DMU). <br/>Per ulteriori informazioni, vedere Unità di [integrazione dati](copy-activity-performance-features.md#data-integration-units). | No |
| parallelCopies | Specificare il parallelismo che si desidera venga utilizzato dall'attività Copy durante la lettura dei dati dall'origine e la scrittura dei dati nel sink.<br/>Per ulteriori informazioni, vedere [Copia parallela](copy-activity-performance-features.md#parallel-copy). | No |
| preserve | Specificare se conservare i metadati/ACL durante la copia dei dati. <br/>Per ulteriori informazioni, consultate [Mantenere i metadati.](copy-activity-preserve-metadata.md) |No |
| enableStaging<br/>stagingSettings | Specificare se eseguire temporaneamente i dati intermedi nell'archivio BLOB anziché copiare direttamente i dati dall'origine al sink.<br/>Per informazioni su scenari utili e dettagli di configurazione, consultate [Copia in fasi.](copy-activity-performance-features.md#staged-copy) | No |
| enableSkipIncompatibleRow<br/>redirectIncompatibleRowSettings| Scegliere come gestire le righe incompatibili quando si copiano i dati dall'origine al sink.<br/>Per ulteriori informazioni, consultate [Tolleranza di errore.](copy-activity-fault-tolerance.md) | No |

## <a name="monitoring"></a>Monitoraggio

È possibile monitorare l'attività di copia eseguita in Azure Data Factory sia visivamente che a livello di codice. Per informazioni dettagliate, vedere [Monitorare l'attività](copy-activity-monitoring.md)di copia .

## <a name="incremental-copy"></a>Copia incrementale

Data Factory consente di copiare in modo incrementale i dati differenziali da un archivio dati di origine a un archivio dati sink. Per informazioni dettagliate, vedere [Esercitazione: Copiare in modo incrementale i dati](tutorial-incremental-copy-overview.md).

## <a name="performance-and-tuning"></a>Prestazioni e ottimizzazione

L'esperienza di [monitoraggio dell'attività](copy-activity-monitoring.md) di copia mostra le statistiche sulle prestazioni di copia per ogni esecuzione dell'attività. La [guida Copia attività e scalabilità](copy-activity-performance.md) descrive i fattori chiave che influiscono sulle prestazioni dello spostamento dei dati tramite l'attività Copia in Azure Data Factory.The Copy activity performance and scalability guide describes key factors that affect the performance of data movement via the Copy activity in Azure Data Factory. Vengono inoltre elencati i valori delle prestazioni osservati durante il test e viene illustrato come ottimizzare le prestazioni dell'attività di copia.

## <a name="resume-from-last-failed-run"></a>Riprendere dall'ultima esecuzione non riuscitaResume from last failed run

L'attività di copia supporta la ripresa dall'ultima esecuzione non riuscita quando si copiano file di grandi dimensioni con il formato binario tra gli archivi basati su file e si sceglie di mantenere la gerarchia cartella/file dall'origine al sink, ad esempio per eseguire la migrazione dei dati da Amazon S3 ad Azure Data Lake Storage Gen2. Si applica ai seguenti connettori basati su file: [Amazon S3](connector-amazon-simple-storage-service.md), Blob di [Azure](connector-azure-blob-storage.md), Azure Data Lake [Storage Gen1](connector-azure-data-lake-store.md), Azure Data Lake Storage [Gen2](connector-azure-data-lake-storage.md), [Archiviazione file di Azure](connector-azure-file-storage.md), File [System](connector-file-system.md), [FTP](connector-ftp.md), Google [Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md)e [SFTP](connector-sftp.md).

È possibile sfruttare il curriculum sull'attività di copia nei due modi seguenti:

- **Nuovo tentativo a livello di attività:Activity level retry:** È possibile impostare il conteggio tentativi per l'attività di copia. Durante l'esecuzione della pipeline, se l'esecuzione dell'attività di copia non riesce, il successivo tentativo automatico inizierà dal punto di errore dell'ultima versione di valutazione.
- **Riesecuzione dall'attività non riuscita:** Dopo il completamento dell'esecuzione della pipeline, è anche possibile attivare una riesecuzione dall'attività non riuscita nella visualizzazione di monitoraggio dell'interfaccia utente ADF o a livello di codice. Se l'attività non riuscita è un'attività di copia, la pipeline non solo verrà rieseguita da questa attività, ma verrà ripresa anche dal punto di errore dell'esecuzione precedente.

    ![Copia curriculum](media/copy-activity-overview/resume-copy.png)

Pochi punti da notare:

- Il curriculum avviene a livello di file. Se l'attività di copia non riesce durante la copia di un file, nella prossima esecuzione, questo file specifico verrà ricopiato.
- Affinché riprenda funzioni correttamente, non modificare le impostazioni dell'attività di copia tra le repliche.
- Quando si copiano dati da Amazon S3, Blob di Azure, Azure Data Lake Storage Gen2 e Google Cloud Storage, l'attività di copia può riprendere dal numero arbitrario di file copiati. Mentre per il resto dei connettori basati su file come origine, l'attività di copia corrente supporta la ripresa da un numero limitato di file, in genere a decine di migliaia e varia a seconda della lunghezza dei percorsi dei file; i file oltre questo numero verranno ricopiati durante le repliche.

Per altri scenari oltre alla copia binaria dei file, la riesecuzione dell'attività di copia inizia dall'inizio.

## <a name="preserve-metadata-along-with-data"></a>Conservare i metadati insieme ai dati

Durante la copia dei dati dall'origine al sink, in scenari come la migrazione di data lake, è anche possibile scegliere di mantenere i metadati e gli ACL insieme ai dati tramite l'attività di copia. Consultate [Conservare i metadati](copy-activity-preserve-metadata.md) per i dettagli.

## <a name="schema-and-data-type-mapping"></a>Mapping dello schema e dei tipi di dati

Vedere [Mapping dello schema e dei tipi](copy-activity-schema-and-type-mapping.md) di dati per informazioni sul modo in cui l'attività di copia esegue il mapping dei dati di origine al sink.

## <a name="add-additional-columns-during-copy"></a>Aggiungere altre colonne durante la copia

Oltre a copiare i dati dall'archivio dati di origine al sink, è anche possibile configurare l'aggiunta di altre colonne di dati da copiare nel sink. Ad esempio:

- Quando si esegue la copia dall'origine basata su file, archiviare il percorso relativo del file come colonna aggiuntiva per tracciare da quale file provengono i dati.
- Aggiungere una colonna con espressione ADF per collegare variabili di sistema ADF come il nome della pipeline/id pipeline o archiviare altri valori dinamici dall'output dell'attività a monte.
- Aggiungere una colonna con valore statico per soddisfare le esigenze di consumo a valle.

Nella scheda Origine attività di copia è possibile trovare la seguente configurazione: 

![Aggiungere altre colonne nell'attività di copia](./media/copy-activity-overview/copy-activity-add-additional-columns.png)

>[!TIP]
>Questa funzionalità funziona con il modello di set di dati più recente. Se questa opzione non è disponibile nell'interfaccia utente, provare a creare un nuovo set di dati.

Per configurarlo a livello `additionalColumns` di codice, aggiungere la proprietà nell'origine dell'attività di copia:To configure it programmatically, add the property in your copy activity source:

| Proprietà | Descrizione | Obbligatoria |
| --- | --- | --- |
| additionalColumns (Colonne aggiuntive) | Aggiungere altre colonne di dati da copiare nel sink.<br><br>Ogni oggetto `additionalColumns` nella matrice rappresenta una colonna aggiuntiva. L'oggetto `name` definisce il `value` nome della colonna e indica il valore dei dati di tale colonna.<br><br>I valori dei dati consentiti sono:<br>- **`$$FILEPATH`**- una variabile riservata indica di archiviare il percorso relativo dei file di origine al percorso della cartella specificato nel set di dati. Applica all'origine basata su file.<br>- **Espressione**<br>- **Valore statico** | No |

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

## <a name="fault-tolerance"></a>Tolleranza di errore

Per impostazione predefinita, l'attività di copia interrompe la copia dei dati e restituisce un errore quando le righe di dati di origine non sono compatibili con le righe di dati sink. Per fare in modo che la copia abbia esito positivo, è possibile configurare l'attività Copia in modo che salti e registri le righe incompatibili e copi solo i dati compatibili. Per informazioni dettagliate, vedere [Tolleranza di errore dell'attività](copy-activity-fault-tolerance.md) di copia.

## <a name="next-steps"></a>Passaggi successivi
Vedere le guide rapide, le esercitazioni e gli esempi seguenti:

- [Copiare i dati da una posizione a un'altra nello stesso account di archiviazione BLOB di AzureCopy data from one location to another location in the same Azure Blob storage account](quickstart-create-data-factory-dot-net.md)
- [Copiare i dati dall'archiviazione BLOB di Azure al database SQL di AzureCopy data from Azure Blob storage to Azure SQL Database](tutorial-copy-data-dot-net.md)
- [Copiare dati da un database di SQL Server locale in AzureCopy data from an on-premises SQL Server database to Azure](tutorial-hybrid-copy-powershell.md)

---
title: Copiare dati da e verso Azure Databricks Delta Lake
description: Informazioni su come copiare dati da e verso Azure Databricks Delta Lake usando un'attività di copia in una pipeline di Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/28/2020
ms.openlocfilehash: 4ff1a793b3e8c4fe642aa304f1aa59bd8edefb8c
ms.sourcegitcommit: ada9a4a0f9d5dbb71fc397b60dc66c22cf94a08d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/28/2020
ms.locfileid: "91405618"
---
# <a name="copy-data-to-and-from-azure-databricks-delta-lake-by-using-azure-data-factory"></a>Copiare dati da e verso Azure Databricks Delta Lake usando Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da e verso Azure Databricks Delta Lake. Si basa sull' [attività di copia in Azure Data Factory](copy-activity-overview.md) articolo, che presenta una panoramica generale dell'attività di copia.

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo Azure Databricks connettore Delta Lake è supportato per le attività seguenti:

- [Attività di copia](copy-activity-overview.md) con una tabella di [matrice di origine/sink supportata](copy-activity-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)

In generale, Azure Data Factory supporta Delta Lake con le funzionalità seguenti per soddisfare le varie esigenze.

- L'attività di copia supporta Azure Databricks connettore Delta Lake per copiare i dati da qualsiasi archivio dati di origine supportato in Azure Databricks tabella Delta Lake e dalla tabella Delta Lake a qualsiasi archivio dati di sink supportato. Si avvale del cluster databricks per eseguire lo spostamento dei dati, vedere la [sezione relativa ai prerequisiti in Prerequisiti](#prerequisites).
- Il [flusso di dati del mapping](concepts-data-flow-overview.md) supporta il [formato Delta](format-delta.md) generico in archiviazione di Azure come origine e sink per leggere e scrivere file differenziali per ETL senza codice e viene eseguito su Azure Integration Runtime gestiti.
- Le [attività di databricks](transform-data-databricks-notebook.md) supportano la orchestrazione del carico di lavoro ETL incentrato sul codice o di machine learning su Delta Lake.

## <a name="prerequisites"></a>Prerequisiti

Per usare questo connettore Azure Databricks Delta Lake, è necessario configurare un cluster in Azure Databricks.

- Per copiare dati in Delta Lake, l'attività di copia richiama Azure Databricks cluster per leggere i dati da un'archiviazione di Azure, che è l'origine originale o un'area di gestione temporanea in cui Data Factory innanzitutto scrive i dati di origine tramite una copia di gestione temporanea incorporata. Scopri di più da [Delta Lake come origine](#delta-lake-as-source).
- Analogamente, per copiare dati da Delta Lake, l'attività di copia richiama Azure Databricks cluster per scrivere i dati in una risorsa di archiviazione di Azure, ovvero il sink originale o un'area di gestione temporanea da cui Data Factory continua a scrivere i dati nel sink finale tramite una copia temporanea incorporata. Altre informazioni sono disponibili in [Delta Lake come sink](#delta-lake-as-sink).

Il cluster databricks deve avere accesso all'account BLOB o Azure Data Lake Storage Gen2 di Azure, sia il contenitore di archiviazione/file system usato per l'origine/sink/gestione temporanea e il contenitore/file system in cui si vogliono scrivere le tabelle Delta Lake.

- Per usare **Azure Data Lake storage Gen2**, è possibile configurare un' **entità servizio** o una **chiave di accesso dell'account di archiviazione** nel cluster databricks come parte della configurazione del Apache Spark. Seguire la procedura in [accedere direttamente con l'entità servizio](https://docs.microsoft.com/azure/databricks/data/data-sources/azure/azure-datalake-gen2#--access-directly-with-service-principal-and-oauth-20) o [accedere direttamente usando la chiave di accesso dell'account di archiviazione](https://docs.microsoft.com/azure/databricks/data/data-sources/azure/azure-datalake-gen2#--access-directly-using-the-storage-account-access-key).

- Per usare l' **archiviazione BLOB di Azure**, è possibile configurare una **chiave di accesso dell'account di archiviazione** o un **token** di firma di accesso condiviso nel cluster databricks come parte della configurazione del Apache Spark. Seguire i passaggi in [accedere all'archiviazione BLOB di Azure con l'API RDD](https://docs.microsoft.com/azure/databricks/data/data-sources/azure/azure-storage#access-azure-blob-storage-using-the-rdd-api).

Durante l'esecuzione dell'attività di copia, se il cluster configurato è stato terminato, Data Factory lo avvia automaticamente. Se si crea una pipeline usando Data Factory interfaccia utente di creazione, per operazioni come l'anteprima dei dati, è necessario disporre di un cluster attivo, Data Factory non avvierà il cluster per conto dell'utente.

#### <a name="specify-the-cluster-configuration"></a>Specificare la configurazione del cluster

1. Nell'elenco a discesa **modalità cluster** selezionare **standard**.

2. Nell'elenco a discesa **Databricks Runtime versione** selezionare una versione di runtime di databricks.

3. Attivare l' [ottimizzazione automatica](https://docs.microsoft.com/azure/databricks/delta/optimizations/auto-optimize) aggiungendo le proprietà seguenti alla configurazione di [Spark](https://docs.microsoft.com/azure/databricks/clusters/configure#spark-config):

   ```
   spark.databricks.delta.optimizeWrite.enabled true
   spark.databricks.delta.autoCompact.enabled true
   ```

4. Configurare il cluster in base alle esigenze di integrazione e scalabilità.

Per informazioni dettagliate sulla configurazione del cluster, vedere [configurare i cluster](https://docs.microsoft.com/azure/databricks/clusters/configure).

## <a name="get-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che definiscono Data Factory entità specifiche di un connettore Azure Databricks Delta Lake.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Le proprietà seguenti sono supportate per un servizio collegato Azure Databricks Delta Lake.

| Proprietà    | Descrizione                                                  | Obbligatoria |
| :---------- | :----------------------------------------------------------- | :------- |
| type        | La proprietà Type deve essere impostata su **AzureDatabricksDeltaLake**. | Sì      |
| dominio      | Specificare l'URL dell'area di lavoro Azure Databricks, ad esempio `https://adb-xxxxxxxxx.xx.azuredatabricks.net` . |          |
| clusterId   | Specificare l'ID cluster di un cluster esistente. Deve essere un cluster interattivo già creato. <br>È possibile trovare l'ID cluster di un cluster interattivo nell'area di lavoro di Databricks -> Cluster -> Interactive Cluster Name (Nome cluster interattivo) -> Configurazione -> Tag. [Altre informazioni](https://docs.microsoft.com/azure/databricks/clusters/configure#cluster-tags). |          |
| accessToken | Il token di accesso è obbligatorio per l'autenticazione del data factory con Azure Databricks. Deve essere generato dall'area di lavoro di Databricks. I passaggi più dettagliati per trovare il token di accesso sono disponibili [qui](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/authentication#generate-token). |          |
| connectVia  | [Runtime di integrazione](concepts-integration-runtime.md) usato per la connessione all'archivio dati. È possibile usare il runtime di integrazione di Azure o un runtime di integrazione self-hosted (se l'archivio dati si trova in una rete privata). Se non specificato, viene usato il runtime di integrazione di Azure predefinito. | No       |

**Esempio:**

```json
{
    "name": "AzureDatabricksDeltaLakeLinkedService",
    "properties": {
        "type": "AzureDatabricksDeltaLake",
        "typeProperties": {
            "domain": "https://adb-xxxxxxxxx.xx.azuredatabricks.net",
            "clusterId": "<cluster id>",
            "accessToken": {
                "type": "SecureString", 
                "value": "<access token>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di dati, vedere l'articolo [Set di dati](concepts-datasets-linked-services.md). 

Per il set di dati Azure Databricks Delta Lake sono supportate le proprietà seguenti.

| Proprietà  | Descrizione                                                  | Obbligatoria                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| type      | La proprietà Type del set di dati deve essere impostata su **AzureDatabricksDeltaLakeDataset**. | Sì                         |
| database | Nome del database. |No per l'origine, Sì per il sink  |
| table | Nome della tabella Delta. |No per l'origine, Sì per il sink  |

**Esempio:**

```json
{
    "name": "AzureDatabricksDeltaLakeDataset",
    "properties": {
        "type": "AzureDatabricksDeltaLakeDataset",
        "typeProperties": {
            "database": "<database name>",
            "table": "<delta table name>"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "linkedServiceName": {
            "referenceName": "<name of linked service>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine e dal sink Azure Databricks Delta Lake.

### <a name="delta-lake-as-source"></a>Delta Lake come origine

Per copiare dati da Azure Databricks Delta Lake, nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti.

| Proprietà                     | Descrizione                                                  | Obbligatoria |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| type                         | La proprietà Type dell'origine dell'attività di copia deve essere impostata su **AzureDatabricksDeltaLakeSource**. | Sì      |
| query          | Specificare la query SQL per leggere i dati. Per il controllo Time Travel, seguire il modello seguente:<br>- `SELECT * FROM events TIMESTAMP AS OF timestamp_expression`<br>- `SELECT * FROM events VERSION AS OF version` | No       |
| exportSettings | Impostazioni avanzate utilizzate per recuperare i dati dalla tabella Delta. | No       |
| ***In `exportSettings` :*** |  |  |
| tipo | Tipo di comando Export impostato su **AzureDatabricksDeltaLakeExportCommand**. | Sì |
| dateFormat | Formattare il tipo di data in stringa con un formato di data. I formati di data personalizzati seguono i formati al [modello DateTime](https://spark.apache.org/docs/latest/sql-ref-datetime-pattern.html). Se non è specificato, viene usato il valore predefinito `yyyy-MM-dd` . | No |
| timestampFormat | Formattare il tipo di timestamp in stringa con formato timestamp. I formati di data personalizzati seguono i formati al [modello DateTime](https://spark.apache.org/docs/latest/sql-ref-datetime-pattern.html). Se non è specificato, viene usato il valore predefinito `yyyy-MM-dd'T'HH:mm:ss[.SSS][XXX]` . | No |

#### <a name="direct-copy-from-delta-lake"></a>Copia diretta da Delta Lake

Se l'archivio dati del sink e il formato soddisfano i criteri descritti in questa sezione, è possibile usare l'attività di copia per copiare direttamente da Azure Databricks tabella Delta a sink. Data Factory controlla le impostazioni e non riesce l'esecuzione dell'attività di copia se non vengono soddisfatti i criteri seguenti:

- Il **servizio collegato sink** è un [Archivio BLOB di Azure](connector-azure-blob-storage.md) o [Azure Data Lake storage Gen2](connector-azure-data-lake-storage.md). La credenziale dell'account deve essere preconfigurata in Azure Databricks configurazione del cluster. per ulteriori informazioni, vedere [prerequisiti](#prerequisites).

- Il **formato dei dati sink** è di **parquet**, **testo delimitato**o **avro** con le configurazioni seguenti e punta a una cartella anziché a un file.

    - Per il formato **parquet** , il codec di compressione è **None**, **Snapper**o **gzip**.
    - Per il formato **testo delimitato** :
        - `rowDelimiter` qualsiasi carattere singolo.
        - `compression` può essere **None**, **bzip2**, **gzip**.
        - `encodingName` UTF-7 non è supportato.
    - Per il formato **avro** , il codec di compressione è **None**, **deflate**o **Snapper**.

- Nell'origine dell'attività di copia `additionalColumns` non è specificato.
- Se si copiano dati in testo delimitato, nel sink dell'attività di copia `fileExtension` deve essere ". csv".
- Nel mapping dell'attività di copia, la conversione del tipo non è abilitata.

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromDeltaLake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delta lake input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "AzureDatabricksDeltaLakeSource",
                "sqlReaderQuery": "SELECT * FROM events TIMESTAMP AS OF timestamp_expression"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="staged-copy-from-delta-lake"></a>Copia di gestione temporanea da Delta Lake

Quando l'archivio dati o il formato di sink non corrisponde ai criteri di copia diretta, come indicato nell'ultima sezione, abilitare la copia temporanea incorporata usando un'istanza di archiviazione di Azure provvisoria. La funzionalità copia di staging assicura inoltre una migliore velocità effettiva, Data Factory Esporta i dati da Azure Databricks Delta Lake nell'archivio di gestione temporanea, quindi copia i dati nel sink e infine pulisce i dati temporanei dall'archiviazione di staging. Per informazioni dettagliate sulla copia di dati tramite staging, vedere [copia](copy-activity-performance-features.md#staged-copy) di staging.

Per usare questa funzionalità, creare un [servizio collegato di archiviazione BLOB di Azure](connector-azure-blob-storage.md#linked-service-properties) o [Azure Data Lake storage Gen2 servizio collegato](connector-azure-data-lake-storage.md#linked-service-properties) che fa riferimento all'account di archiviazione come staging provvisorio. Specificare quindi le `enableStaging` `stagingSettings` proprietà e nell'attività di copia.

>[!NOTE]
>La credenziale dell'account di archiviazione di staging deve essere preconfigurata in Azure Databricks configurazione del cluster. per ulteriori informazioni, vedere [prerequisiti](#prerequisites).

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromDeltaLake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delta lake input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "AzureDatabricksDeltaLakeSource",
                "sqlReaderQuery": "SELECT * FROM events TIMESTAMP AS OF timestamp_expression"
            },
            "sink": {
                "type": "<sink type>"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingStorage",
                    "type": "LinkedServiceReference"
                },
                "path": "mystagingpath"
            }
        }
    }
]
```

### <a name="delta-lake-as-sink"></a>Delta Lake come sink

Per copiare dati in Azure Databricks Delta Lake, nella sezione **sink** dell'attività di copia sono supportate le proprietà seguenti.

| Proprietà      | Descrizione                                                  | Obbligatoria |
| :------------ | :----------------------------------------------------------- | :------- |
| type          | Proprietà Type del sink dell'attività di copia, impostata su **AzureDatabricksDeltaLakeSink**. | Sì      |
| preCopyScript | Specificare una query SQL per l'attività di copia da eseguire prima di scrivere i dati nella tabella Delta di databricks in ogni esecuzione. È possibile usare questa proprietà per pulire i dati precaricati o aggiungere un'istruzione TRUNCATE TABLE o vacuum. | No       |
| importSettings | Impostazioni avanzate utilizzate per scrivere dati nella tabella Delta. | No |
| ***In `importSettings` :*** |                                                              |  |
| tipo | Tipo di comando Import impostato su **AzureDatabricksDeltaLakeImportCommand**. | Sì |
| dateFormat | Formattare la stringa in un tipo di data con un formato di data. I formati di data personalizzati seguono i formati al [modello DateTime](https://spark.apache.org/docs/latest/sql-ref-datetime-pattern.html). Se non è specificato, viene usato il valore predefinito `yyyy-MM-dd` . | No |
| timestampFormat | Formattare la stringa in un tipo timestamp con formato timestamp. I formati di data personalizzati seguono i formati al [modello DateTime](https://spark.apache.org/docs/latest/sql-ref-datetime-pattern.html). Se non è specificato, viene usato il valore predefinito `yyyy-MM-dd'T'HH:mm:ss[.SSS][XXX]` . | No |

#### <a name="direct-copy-to-delta-lake"></a>Copia diretta in Delta Lake

Se l'archivio dati di origine e il formato soddisfano i criteri descritti in questa sezione, è possibile usare l'attività di copia per copiare direttamente dall'origine a Azure Databricks Delta Lake. Azure Data Factory controlla le impostazioni e non riesce l'esecuzione dell'attività di copia se non vengono soddisfatti i criteri seguenti:

- Il **servizio collegato di origine** è l' [Archivio BLOB di Azure](connector-azure-blob-storage.md) o [Azure Data Lake storage Gen2](connector-azure-data-lake-storage.md). La credenziale dell'account deve essere preconfigurata in Azure Databricks configurazione del cluster. per ulteriori informazioni, vedere [prerequisiti](#prerequisites).

- Il **formato dei dati di origine** è **parquet**, **testo delimitato**o **avro** con le configurazioni seguenti e punta a una cartella anziché a un file.

    - Per il formato **parquet** , il codec di compressione è **None**, **Snapper**o **gzip**.
    - Per il formato **testo delimitato** :
        - `rowDelimiter` è il valore predefinito o un carattere singolo.
        - `compression` può essere **None**, **bzip2**, **gzip**.
        - `encodingName` UTF-7 non è supportato.
    - Per il formato **avro** , il codec di compressione è **None**, **deflate**o **Snapper**.

- Nell'origine dell'attività di copia: 

    - `wildcardFileName` contiene solo caratteri jolly `*` ma non `?` `wildcardFolderName` specificati.
    - `prefix`, `modifiedDateTimeStart`, `modifiedDateTimeEnd` e `enablePartitionDiscovery` non sono specificati.
    - `additionalColumns` non specificato.

- Nel mapping dell'attività di copia, la conversione del tipo non è abilitata.

**Esempio:**

```json
"activities":[
    {
        "name": "CopyToDeltaLake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Delta lake output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDatabricksDeltaLakeSink"
            }
        }
    }
]
```

#### <a name="staged-copy-to-delta-lake"></a>Copia di gestione temporanea in Delta Lake

Quando l'archivio dati o il formato di origine non corrisponde ai criteri di copia diretta, come indicato nell'ultima sezione, abilitare la copia temporanea incorporata usando un'istanza di archiviazione di Azure provvisoria. La funzionalità copia di staging assicura inoltre una migliore velocità effettiva, Data Factory converte automaticamente i dati per soddisfare i requisiti di formato dei dati nell'archiviazione di staging, quindi carica i dati in Delta Lake da tale posizione. Infine, pulisce i dati temporanei dall'archiviazione. Per informazioni dettagliate sulla copia di dati tramite gestione temporanea, vedere [copia](copy-activity-performance-features.md#staged-copy) di staging.

Per usare questa funzionalità, creare un [servizio collegato di archiviazione BLOB di Azure](connector-azure-blob-storage.md#linked-service-properties) o [Azure Data Lake storage Gen2 servizio collegato](connector-azure-data-lake-storage.md#linked-service-properties) che fa riferimento all'account di archiviazione come staging provvisorio. Specificare quindi le `enableStaging` `stagingSettings` proprietà e nell'attività di copia.

>[!NOTE]
>La credenziale dell'account di archiviazione di staging deve essere preconfigurata in Azure Databricks configurazione del cluster. per ulteriori informazioni, vedere [prerequisiti](#prerequisites).

**Esempio:**

```json
"activities":[
    {
        "name": "CopyToDeltaLake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Delta lake output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDatabricksDeltaLakeSink"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                },
                "path": "mystagingpath"
            }
        }
    }
]
```

## <a name="monitoring"></a>Monitoraggio

Azure Data Factory offre la stessa [esperienza di monitoraggio dell'attività di copia](copy-activity-monitoring.md) degli altri connettori. Inoltre, poiché il caricamento dei dati da/in Delta Lake è in esecuzione nel cluster di Azure Databricks, è possibile visualizzare ulteriormente i [log dettagliati del cluster](https://docs.microsoft.com/azure/databricks/clusters/clusters-manage#--view-cluster-logs) e [monitorare le prestazioni](https://docs.microsoft.com/azure/databricks/clusters/clusters-manage#--monitor-performance).

## <a name="lookup-activity-properties"></a>Proprietà dell'attività Lookup

Per ulteriori informazioni sulle proprietà, vedere [attività Lookup](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Passaggi successivi

Per un elenco di archivi dati supportati come origini e sink per attività di copia in Data Factory, vedere [archivi dati e formati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

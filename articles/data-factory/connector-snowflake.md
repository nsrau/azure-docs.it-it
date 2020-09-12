---
title: Copiare e trasformare i dati in fiocco di neve
description: Informazioni su come copiare e trasformare i dati in fiocco di neve usando Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/28/2020
ms.openlocfilehash: 255fa9e058fdbb3b7edb73e75fd53f4a2490bfca
ms.sourcegitcommit: 70ee014d1706e903b7d1e346ba866f5e08b22761
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2020
ms.locfileid: "90023857"
---
# <a name="copy-and-transform-data-in-snowflake-by-using-azure-data-factory"></a>Copiare e trasformare i dati in fiocco di neve usando Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da e in fiocco di neve e usare il flusso di dati per trasformare i dati in fiocco di neve. Per ulteriori informazioni su Data Factory, vedere l' [articolo introduttivo](introduction.md).

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo connettore a fiocco di neve è supportato per le attività seguenti:

- [Attività di copia](copy-activity-overview.md) con una tabella di [matrice di origine/sink supportata](copy-activity-overview.md)
- [Flusso di dati per mapping](concepts-data-flow-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)

Per l'attività di copia, questo connettore a fiocco di neve supporta le funzioni seguenti:

- Copiare i dati da fiocco di neve che usa la copia di fiocco di neve [nel comando [Location]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html) per ottenere prestazioni ottimali.
- Copiare i dati in fiocco di neve che sfrutta la copia del fiocco di neve [nel comando [Table]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html) per ottenere prestazioni ottimali. Supporta fiocco di neve in Azure. 

Quando si usa l'area di lavoro di Azure sinapsi Analytics, il fiocco di neve non è supportato.

## <a name="get-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che definiscono Data Factory entità specifiche di un connettore a fiocco di neve.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per un servizio collegato a fiocco di neve sono supportate le proprietà seguenti.

| Proprietà         | Descrizione                                                  | Obbligatoria |
| :--------------- | :----------------------------------------------------------- | :------- |
| type             | La proprietà Type deve essere impostata su **fiocco di neve**.              | Sì      |
| connectionString | Specifica le informazioni necessarie per la connessione all'istanza di fiocco di neve. È possibile scegliere di inserire la password o l'intera stringa di connessione in Azure Key Vault. Per altri dettagli, vedere gli esempi sotto la tabella, nonché le [credenziali di archiviazione nell'articolo Azure Key Vault](store-credentials-in-key-vault.md) .<br><br>Alcune impostazioni tipiche:<br>- **Nome account:**  [Nome completo dell'account](https://docs.snowflake.net/manuals/user-guide/connecting.html#your-snowflake-account-name) di fiocco di neve (inclusi i segmenti aggiuntivi che identificano l'area e la piattaforma cloud), ad esempio xy12345. East-US-2. Azure.<br/>- **Nome utente:** Nome dell'account di accesso dell'utente per la connessione.<br>- **Password:** Password per l'utente.<br>- **Database:** Il database predefinito da utilizzare una volta connessi. Deve essere un database esistente per il quale il ruolo specificato dispone dei privilegi.<br>- Data **Warehouse:** Il warehouse virtuale da usare una volta connessi. Deve essere un warehouse esistente per il quale il ruolo specificato dispone dei privilegi.<br>- **Ruolo:** Ruolo di controllo di accesso predefinito da usare nella sessione di fiocco di neve. Il ruolo specificato deve essere un ruolo esistente che è già stato assegnato all'utente specificato. Il ruolo predefinito è PUBLIC. | Sì      |
| connectVia       | [Runtime di integrazione](concepts-integration-runtime.md) usato per la connessione all'archivio dati. È possibile usare il runtime di integrazione di Azure o un runtime di integrazione self-hosted (se l'archivio dati si trova in una rete privata). Se non specificato, viene usato il runtime di integrazione di Azure predefinito. | No       |

**Esempio:**

```json
{
    "name": "SnowflakeLinkedService",
    "properties": {
        "type": "Snowflake",
        "typeProperties": {
            "connectionString": "jdbc:snowflake://<accountname>.snowflakecomputing.com/?user=<username>&db=<database>&warehouse=<warehouse>&role=<myRole>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Password in Azure Key Vault:**

```json
{
    "name": "SnowflakeLinkedService",
    "properties": {
        "type": "Snowflake",
        "typeProperties": {
            "connectionString": "jdbc:snowflake://<accountname>.snowflakecomputing.com/?user=<username>&db=<database>&warehouse=<warehouse>&role=<myRole>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>",
                    "type": "LinkedServiceReference"
                }, 
                "secretName": "<secretName>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di dati, vedere l'articolo [Set di dati](concepts-datasets-linked-services.md). 

Per il set di dati di fiocco di neve sono supportate le proprietà seguenti.

| Proprietà  | Descrizione                                                  | Obbligatoria                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| type      | La proprietà Type del set di dati deve essere impostata su **SnowflakeTable**. | Sì                         |
| schema | Nome dello schema. Si noti che il nome dello schema fa distinzione tra maiuscole e minuscole in ADF. |No per l'origine, Sì per il sink  |
| tabella | Nome della tabella/vista. Si noti che il nome della tabella fa distinzione tra maiuscole e minuscole in ADF. |No per l'origine, Sì per il sink  |

**Esempio:**

```json
{
    "name": "SnowflakeDataset",
    "properties": {
        "type": "SnowflakeTable",
        "typeProperties": {
            "schema": "<Schema name for your Snowflake database>",
            "table": "<Table name for your Snowflake database>"
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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine e dal sink di fiocco di neve.

### <a name="snowflake-as-the-source"></a>Fiocco di neve come origine

Il connettore fiocco di neve usa la copia di fiocco di neve [nel comando [Location]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html) per ottenere prestazioni ottimali.

Se il formato e l'archivio dati sink sono supportati in modo nativo dal comando di copia a fiocco di neve, è possibile usare l'attività di copia per copiare direttamente da fiocco di neve a sink. Per informazioni dettagliate, vedere [copia diretta da fiocco di neve](#direct-copy-from-snowflake). In caso contrario, usare la [copia temporanea incorporata da fiocco di neve](#staged-copy-from-snowflake).

Per copiare dati da fiocco di neve, nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti.

| Proprietà                     | Descrizione                                                  | Obbligatoria |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| type                         | La proprietà Type dell'origine dell'attività di copia deve essere impostata su **SnowflakeSource**. | Sì      |
| query          | Specifica la query SQL per leggere i dati da fiocco di neve. Se i nomi dello schema, della tabella e delle colonne contengono lettere minuscole, indicare l'identificatore di oggetto nella query, ad `select * from "schema"."myTable"` esempio.<br>L'esecuzione di stored procedure non è supportata. | No       |
| exportSettings | Impostazioni avanzate utilizzate per recuperare dati da fiocco di neve. È possibile configurare quelli supportati dal comando COPY into che Data Factory passerà quando si richiama l'istruzione. | No       |
| ***In `exportSettings` :*** |  |  |
| type | Tipo di comando Export impostato su **SnowflakeExportCopyCommand**. | Sì |
| additionalCopyOptions | Opzioni di copia aggiuntive, fornite come dizionario di coppie chiave-valore. Esempi: MAX_FILE_SIZE, overwrite. Per altre informazioni, vedere [Opzioni di copia a fiocco di neve](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html#copy-options-copyoptions). | No |
| additionalFormatOptions | Opzioni del formato di file aggiuntive fornite al comando COPY come dizionario di coppie chiave-valore. Esempi: DATE_FORMAT, TIME_FORMAT, TIMESTAMP_FORMAT. Per altre informazioni, vedere [Opzioni del tipo di formato fiocco di neve](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html#format-type-options-formattypeoptions). | No |

#### <a name="direct-copy-from-snowflake"></a>Copia diretta da fiocco di neve

Se l'archivio dati sink e il formato soddisfano i criteri descritti in questa sezione, è possibile usare l'attività di copia per copiare direttamente da fiocco di neve a sink. Data Factory controlla le impostazioni e non riesce l'esecuzione dell'attività di copia se non vengono soddisfatti i criteri seguenti:

- Il **servizio collegato sink** è l' [**Archivio BLOB di Azure**](connector-azure-blob-storage.md) con l'autenticazione della **firma di accesso condiviso** .

- Il **formato dei dati sink** è di **parquet**, **testo delimitato**o **JSON** con le configurazioni seguenti:

    - Per il formato **parquet** , il codec di compressione è **None**, **Snapper**o **LZO**.
    - Per il formato **testo delimitato** :
        - `rowDelimiter` è **\r\n**o qualsiasi carattere singolo.
        - `compression` non può essere di **compressione**, **gzip**, **bzip2**o **deflate**.
        - `encodingName` è impostato sul valore predefinito o su **utf-8**.
        - `quoteChar`**virgolette doppie**, **virgolette singole**o **stringhe vuote** (senza virgolette).
    - Per il formato **JSON** , la copia diretta supporta solo il caso in cui la tabella o il risultato della query di un fiocco di codice sorgente abbia solo una singola colonna e il tipo di dati di questa colonna è **Variant**, **Object**o **Array**.
        - `compression` non può essere di **compressione**, **gzip**, **bzip2**o **deflate**.
        - `encodingName` è impostato sul valore predefinito o su **utf-8**.
        - `filePattern` nel sink dell'attività di copia viene lasciato come predefinito o impostato su **setOfObjects**.

- Nell'origine dell'attività di copia `additionalColumns` non è specificato.
- Il mapping delle colonne non è specificato.

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromSnowflake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Snowflake input dataset name>",
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
                "type": "SnowflakeSource",
                "sqlReaderQuery": "SELECT * FROM MYTABLE",
                "exportSettings": {
                    "type": "SnowflakeExportCopyCommand",
                    "additionalCopyOptions": {
                        "MAX_FILE_SIZE": "64000000",
                        "OVERWRITE": true
                    },
                    "additionalFormatOptions": {
                        "DATE_FORMAT": "'MM/DD/YYYY'"
                    }
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="staged-copy-from-snowflake"></a>Copia di gestione temporanea da fiocco di neve

Quando l'archivio dati sink o il formato non è compatibile a livello nativo con il comando di copia a fiocco di neve, come indicato nella sezione precedente, abilitare la copia temporanea incorporata usando un'istanza di archiviazione BLOB di Azure provvisoria. La funzionalità copia di staging assicura inoltre una migliore velocità effettiva, Data Factory Esporta dati da fiocco di neve nell'archivio di gestione temporanea, quindi copia i dati nel sink e infine pulisce i dati temporanei dall'archiviazione di staging. Per informazioni dettagliate sulla copia di dati tramite staging, vedere [copia](copy-activity-performance-features.md#staged-copy) di staging.

Per usare questa funzionalità, creare un [servizio collegato di archiviazione BLOB di Azure](connector-azure-blob-storage.md#linked-service-properties) che fa riferimento all'account di archiviazione di Azure come staging provvisorio. Specificare quindi le `enableStaging` `stagingSettings` proprietà e nell'attività di copia.

> [!NOTE]
> Il servizio collegato di archiviazione BLOB di Azure di staging deve usare l'autenticazione della firma di accesso condiviso, come richiesto dal comando di copia a fiocco di neve. 

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromSnowflake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Snowflake input dataset name>",
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
                "type": "SnowflakeSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
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

### <a name="snowflake-as-sink"></a>Fiocco di neve come sink

Il connettore fiocco di neve usa la copia di fiocco di neve [nel comando [Table]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html) per ottenere prestazioni ottimali. Supporta la scrittura di dati in fiocco di neve in Azure.

Se l'archivio dati di origine e il formato sono supportati in modo nativo dal comando di copia a fiocco di neve, è possibile usare l'attività di copia per copiare direttamente dall'origine a fiocco di neve Per informazioni dettagliate, vedere [copia diretta in fiocco di neve](#direct-copy-to-snowflake). In caso contrario, usare la [copia temporanea incorporata in fiocco di neve](#staged-copy-to-snowflake).

Per copiare dati in fiocco di neve, sono supportate le proprietà seguenti nella sezione **sink** dell'attività di copia.

| Proprietà          | Descrizione                                                  | Obbligatoria                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| type              | Proprietà Type del sink dell'attività di copia, impostata su **SnowflakeSink**. | Sì                                           |
| preCopyScript     | Specificare una query SQL per l'attività di copia da eseguire prima di scrivere i dati in fiocco di neve in ogni esecuzione. Usare questa proprietà per pulire i dati precaricati. | No                                            |
| importSettings | Impostazioni avanzate utilizzate per scrivere dati in fiocco di neve. È possibile configurare quelli supportati dal comando COPY into che Data Factory passerà quando si richiama l'istruzione. | No |
| ***In `importSettings` :*** |                                                              |  |
| type | Tipo di comando Import impostato su **SnowflakeImportCopyCommand**. | Sì |
| additionalCopyOptions | Opzioni di copia aggiuntive, fornite come dizionario di coppie chiave-valore. Esempi: ON_ERROR, FORCE, LOAD_UNCERTAIN_FILES. Per altre informazioni, vedere [Opzioni di copia a fiocco di neve](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html#copy-options-copyoptions). | No |
| additionalFormatOptions | Opzioni del formato di file aggiuntive fornite al comando COPY, fornite come dizionario di coppie chiave-valore. Esempi: DATE_FORMAT, TIME_FORMAT, TIMESTAMP_FORMAT. Per altre informazioni, vedere [Opzioni del tipo di formato fiocco di neve](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html#format-type-options-formattypeoptions). | No |

#### <a name="direct-copy-to-snowflake"></a>Copia diretta in fiocco di neve

Se l'archivio dati di origine e il formato soddisfano i criteri descritti in questa sezione, è possibile usare l'attività di copia per copiare direttamente dall'origine a fiocco di neve. Azure Data Factory controlla le impostazioni e non riesce l'esecuzione dell'attività di copia se non vengono soddisfatti i criteri seguenti:

- Il **servizio collegato di origine** è l' [**Archivio BLOB di Azure**](connector-azure-blob-storage.md) con l'autenticazione della **firma di accesso condiviso** .

- Il **formato dei dati di origine** è **parquet**, **testo delimitato**o **JSON** con le configurazioni seguenti:

    - Per il formato **parquet** , il codec di compressione è **None**o **Snapper**.

    - Per il formato **testo delimitato** :
        - `rowDelimiter` è **\r\n**o qualsiasi carattere singolo. Se il delimitatore di riga non è "\r\n", `firstRowAsHeader` deve essere **false**e `skipLineCount` non è specificato.
        - `compression` non può essere di **compressione**, **gzip**, **bzip2**o **deflate**.
        - `encodingName` viene lasciato come predefinito o impostato su "UTF-8", "UTF-16", "UTF-16BE", "UTF-32", "UTF-32BE", "BIG5", "EUC-JP", "EUC-KR", "GB18030", "ISO-2022-JP", "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "WINDOWS-1250", "WINDOWS-1251", "WINDOWS-1252", "WINDOWS-1253", "WINDOWS-1254", "WINDOWS-1255".
        - `quoteChar`**virgolette doppie**, **virgolette singole**o **stringhe vuote** (senza virgolette).
    - Per il formato **JSON** , la copia diretta supporta solo le maiuscole/minuscole che la tabella di fiocco di neve ha una sola colonna e il tipo di dati di questa colonna è **Variant**, **Object**o **Array**.
        - `compression` non può essere di **compressione**, **gzip**, **bzip2**o **deflate**.
        - `encodingName` è impostato sul valore predefinito o su **utf-8**.
        - Il mapping delle colonne non è specificato.

- Nell'origine dell'attività di copia: 

   -  `additionalColumns` non specificato.
   - Se l'origine è una cartella, `recursive` è impostato su true.
   - `prefix`, `modifiedDateTimeStart`, `modifiedDateTimeEnd` e `enablePartitionDiscovery` non sono specificati.

**Esempio:**

```json
"activities":[
    {
        "name": "CopyToSnowflake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Snowflake output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SnowflakeSink",
                "importSettings": {
                    "type": "SnowflakeImportCopyCommand",
                    "copyOptions": {
                        "FORCE": "TRUE",
                        "ON_ERROR": "SKIP_FILE",
                    },
                    "fileFormatOptions": {
                        "DATE_FORMAT": "YYYY-MM-DD",
                    }
                }
            }
        }
    }
]
```

#### <a name="staged-copy-to-snowflake"></a>Copia di gestione temporanea in fiocco di neve

Quando l'archivio dati sink o il formato non è compatibile a livello nativo con il comando di copia a fiocco di neve, come indicato nella sezione precedente, abilitare la copia temporanea incorporata usando un'istanza di archiviazione BLOB di Azure provvisoria. La funzionalità copia di staging assicura inoltre una migliore velocità effettiva, Data Factory converte automaticamente i dati in modo da soddisfare i requisiti di formato dati di fiocco di neve. Richiama quindi il comando COPY per caricare i dati in fiocco di neve. Infine, pulisce i dati temporanei dall'archiviazione BLOB. Per informazioni dettagliate sulla copia di dati tramite gestione temporanea, vedere [copia](copy-activity-performance-features.md#staged-copy) di staging.

Per usare questa funzionalità, creare un [servizio collegato di archiviazione BLOB di Azure](connector-azure-blob-storage.md#linked-service-properties) che fa riferimento all'account di archiviazione di Azure come staging provvisorio. Specificare quindi le `enableStaging` `stagingSettings` proprietà e nell'attività di copia.

> [!NOTE]
> Il servizio collegato di archiviazione BLOB di Azure di staging deve usare l'autenticazione della firma di accesso condiviso come richiesto dal comando per la copia a fiocco di neve.

**Esempio:**

```json
"activities":[
    {
        "name": "CopyToSnowflake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Snowflake output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SnowflakeSink"
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

## <a name="mapping-data-flow-properties"></a>Proprietà del flusso di dati per mapping

Quando si trasformano i dati nel flusso di dati di mapping, è possibile leggere e scrivere nelle tabelle in fiocco di neve. Per altre informazioni, vedere la [trasformazione origine](data-flow-source.md) e la [trasformazione sink](data-flow-sink.md) nei flussi di dati per mapping. È possibile scegliere di usare un set di dati a fiocco di neve o un [set di dati inline](data-flow-source.md#inline-datasets) come tipo di origine e sink.

### <a name="source-transformation"></a>Trasformazione origine

La tabella seguente elenca le proprietà supportate dall'origine fiocco di neve. È possibile modificare queste proprietà nella scheda **Opzioni di origine** . Il connettore usa il [trasferimento dei dati interni](https://docs.snowflake.com/en/user-guide/spark-connector-overview.html#internal-data-transfer)a fiocco di neve.

| Nome | Descrizione | Obbligatoria | Valori consentiti | Proprietà script flusso di dati |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Tabella | Se si seleziona tabella come input, il flusso di dati recupererà tutti i dati dalla tabella specificata nel set di dati a fiocco di neve o nelle opzioni di origine quando si usa il set di dati inline. | No | string | *(solo per set di dati inline)*<br>tableName<br>schemaName |
| Query | Se si seleziona query come input, immettere una query per recuperare i dati da fiocco di neve. Questa impostazione esegue l'override di qualsiasi tabella scelta nel set di dati.<br>Se i nomi dello schema, della tabella e delle colonne contengono lettere minuscole, indicare l'identificatore di oggetto nella query, ad `select * from "schema"."myTable"` esempio. | No | string | query |

#### <a name="snowflake-source-script-examples"></a>Esempi di script di origine fiocco di neve

Quando si usa il set di dati fiocco di neve come tipo di origine, lo script flusso di dati associato è:

```
source(allowSchemaDrift: true,
    validateSchema: false,
    query: 'select * from MYTABLE',
    format: 'query') ~> SnowflakeSource
```

Se si usa il set di dati inline, lo script del flusso di dati associato è:

```
source(allowSchemaDrift: true,
    validateSchema: false,
    format: 'query',
    query: 'select * from MYTABLE',
    store: 'snowflake') ~> SnowflakeSource
```

### <a name="sink-transformation"></a>Trasformazione sink

La tabella seguente elenca le proprietà supportate dal sink di fiocco di neve. È possibile modificare queste proprietà nella scheda **Impostazioni** . Quando si usa il set di dati inline, verranno visualizzate impostazioni aggiuntive, che corrispondono alle proprietà descritte nella sezione [Proprietà set di dati](#dataset-properties) . Il connettore usa il [trasferimento dei dati interni](https://docs.snowflake.com/en/user-guide/spark-connector-overview.html#internal-data-transfer)a fiocco di neve.

| Nome | Descrizione | Obbligatoria | Valori consentiti | Proprietà script flusso di dati |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Update (metodo) | Specificare le operazioni consentite nella destinazione di fiocco di neve.<br>Per aggiornare, Upsert o eliminare righe, è necessaria una [trasformazione alter Row](data-flow-alter-row.md) per contrassegnare le righe per tali azioni. | Sì | `true` o `false` | cancellabile <br/>inseribile <br/>aggiornabile <br/>upsertable |
| Colonne chiave | Per le operazioni di aggiornamento, upsert ed eliminazione è necessario impostare una o più colonne chiave per determinare quale riga modificare. | No | Array | chiavi |
| azione Tabella | Determina se ricreare o rimuovere tutte le righe dalla tabella di destinazione prima della scrittura.<br>- **None**: nessuna azione verrà eseguita nella tabella.<br>- **Ricrea**: la tabella viene eliminata e ricreata. Questa opzione è obbligatoria se si crea una nuova tabella in modo dinamico.<br>- **Truncate**: tutte le righe della tabella di destinazione vengono rimosse. | No | `true` o `false` | ricreare<br/>truncate |

#### <a name="snowflake-sink-script-examples"></a>Esempi di script di sink di fiocchi di neve

Quando si usa il set di dati fiocco come tipo di sink, lo script del flusso di dati associato è:

```
IncomingStream sink(allowSchemaDrift: true,
    validateSchema: false,
    deletable:true,
    insertable:true,
    updateable:true,
    upsertable:false,
    keys:['movieId'],
    format: 'table',
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> SnowflakeSink
```

Se si usa il set di dati inline, lo script del flusso di dati associato è:

```
IncomingStream sink(allowSchemaDrift: true,
    validateSchema: false,
    format: 'table',
    tableName: 'table',
    schemaName: 'schema',
    deletable: true,
    insertable: true,
    updateable: true,
    upsertable: false,
    store: 'snowflake',
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> SnowflakeSink
```

## <a name="lookup-activity-properties"></a>Proprietà dell'attività Lookup

Per ulteriori informazioni sulle proprietà, vedere [attività Lookup](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Passaggi successivi

Per un elenco di archivi dati supportati come origini e sink per attività di copia in Data Factory, vedere [archivi dati e formati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

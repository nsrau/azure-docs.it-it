---
title: Copiare dati da e in Oracle usando Azure Data Factory | Microsoft Docs
description: Informazioni su come copiare dati da archivi di origine supportati in un database Oracle o da Oracle in archivi di sink supportati, usando Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/23/2019
ms.author: jingwang
ms.openlocfilehash: 90adacffd947be38b447117bfe64242bed3a90af
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/03/2019
ms.locfileid: "70231367"
---
# <a name="copy-data-from-and-to-oracle-by-using-azure-data-factory"></a>Copiare dati da e in Oracle usando Azure Data Factory
> [!div class="op_single_selector" title1="Selezionare uSelezionare la versione del servizio di Azure Data Factory in uso:"]
> * [Versione 1](v1/data-factory-onprem-oracle-connector.md)
> * [Versione corrente](connector-oracle.md)

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da e in un database Oracle. Si basa sulla [Panoramica dell'attività di copia](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Funzionalità supportate

È possibile copiare dati da un database Oracle in qualsiasi archivio dati di sink supportato. È anche possibile copiare dati da qualsiasi archivio di dati di origine supportato in un database Oracle. Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

In particolare, questo connettore Oracle supporta:

- Le versioni seguenti di un database Oracle:
    - Oracle 18C R1 (18,1) e versioni successive
    - Oracle 12C R1 (12,1) e versioni successive
    - Oracle 11g R1 (11,1) e versioni successive
    - Oracle 10g R1 (10,1) e versioni successive
    - Oracle 9i R2 (9,2) e versioni successive
    - Oracle 8i R3 (8.1.7) e versioni successive
    - Servizio Exadata cloud Oracle Database
- Copia di dati tramite l'autenticazione di base o OID.
- Copia parallela da un'origine Oracle. Per informazioni dettagliate, vedere la sezione [copia parallela da Oracle](#parallel-copy-from-oracle) .

> [!Note]
> Il server proxy Oracle non è supportato.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)] 

Il runtime di integrazione fornisce un driver Oracle incorporato. Non è pertanto necessario installare manualmente un driver quando si copiano dati da e in Oracle.

## <a name="get-started"></a>Attività iniziali

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà usate per definire entità di Data Factory specifiche per il connettore Oracle.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Il servizio collegato Oracle supporta le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su **Oracle**. | Yes |
| connectionString | Specifica le informazioni necessarie per la connessione all'istanza del database Oracle. <br/>Contrassegnare questo campo come `SecureString` per archiviarlo in modo sicuro in data factory. È anche possibile inserire una password in Azure Key Vault ed estrarre la `password` configurazione dalla stringa di connessione. Per informazioni dettagliate, vedere gli esempi seguenti e [archiviare le credenziali in Azure Key Vault](store-credentials-in-key-vault.md) . <br><br>**Tipo di connessione supportato**: per identificare il database, è possibile usare il **SID Oracle** o il **nome del servizio Oracle**:<br>- Se si usa il SID: `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;`<br>- Se si usa il nome del servizio: `Host=<host>;Port=<port>;ServiceName=<servicename>;User Id=<username>;Password=<password>;` | Sì |
| connectVia | [Runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. Ulteriori informazioni sono disponibili nella sezione [prerequisiti](#prerequisites) . Se questa proprietà non è specificata, viene usato il tipo Azure Integration Runtime predefinito. |No |

>[!TIP]
>Se viene ricevuto un errore, "ORA-01025: Parametro UPI non compreso nell'intervallo "e la versione Oracle è 8i, aggiungere `WireProtocolMode=1` alla stringa di connessione. Quindi riprovare.

Per abilitare la crittografia sulla connessione di Oracle, sono disponibili due opzioni:

-   Per usare la **crittografia Triple DES (3DES) e la Advanced Encryption Standard (AES)** , sul lato server Oracle passare a Oracle Advanced Security (OAS) e configurare le impostazioni di crittografia. Per informazioni dettagliate, vedere la [documentazione di Oracle](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759). Il connettore ADF (Application Development Framework) Oracle negozia automaticamente il metodo di crittografia per usare quello configurato in OAS quando si stabilisce una connessione a Oracle.

-   Per usare **SSL**:

    1.  Ottenere le informazioni sul certificato SSL. Ottenere le informazioni sul certificato con codifica Distinguished Encoding Rules (DER) del certificato SSL e salvare l'output (-----Begin certificate... End Certificate -----) come file di testo.

        ```
        openssl x509 -inform DER -in [Full Path to the DER Certificate including the name of the DER Certificate] -text
        ```

        **Esempio:** Estrarre le informazioni sul certificato da DERcert. cer e quindi salvare l'output in cert. txt.

        ```
        openssl x509 -inform DER -in DERcert.cer -text
        Output:
        -----BEGIN CERTIFICATE-----
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXX
        -----END CERTIFICATE-----
        ```
    
    2.  `keystore` Compilare o .`truststore` Il comando che segue crea `truststore` il file, con o senza una password, in formato PKCS-12.

        ```
        openssl pkcs12 -in [Path to the file created in the previous step] -out [Path and name of TrustStore] -passout pass:[Keystore PWD] -nokeys -export
        ```

        **Esempio:** Creare un file `truststore` PKCS12, denominato MyTrustStoreFile, con una password.

        ```
        openssl pkcs12 -in cert.txt -out MyTrustStoreFile -passout pass:ThePWD -nokeys -export  
        ```

    3.  Inserire il `truststore` file nel computer IR indipendente. Inserire, ad esempio, il file in C:\MyTrustStoreFile.
    4.  In Azure Data Factory configurare la stringa di connessione Oracle con `EncryptionMethod=1` e il valore `TrustStore`corrispondente /. `TrustStorePassword` Ad esempio `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;EncryptionMethod=1;TrustStore=C:\\MyTrustStoreFile;TrustStorePassword=<trust_store_password>`.

**Esempio:**

```json
{
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Esempio: archiviare la password in Azure Key Vault**

```json
{
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;"
            },
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
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

Questa sezione presenta un elenco delle proprietà supportate dal set di dati Oracle. Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di impostazioni, vedere [Datasets](concepts-datasets-linked-services.md). 

Per copiare dati da e in Oracle, impostare la proprietà Type del set di dati `OracleTable`su. Sono supportate le proprietà seguenti.

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà Type del set di dati deve essere impostata `OracleTable`su. | Sì |
| tableName |Nome della tabella nel database Oracle a cui fa riferimento il servizio collegato. | Sì |

**Esempio:**

```json
{
    "name": "OracleDataset",
    "properties":
    {
        "type": "OracleTable",
        "linkedServiceName": {
            "referenceName": "<Oracle linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Questa sezione presenta un elenco delle proprietà supportate dall'origine e dal sink Oracle. Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). 

### <a name="oracle-as-source"></a>Oracle come origine

>[!TIP]
>Per caricare in modo efficiente i dati da Oracle usando il partizionamento dei dati, vedere la [copia parallela da Oracle](#parallel-copy-from-oracle).

Per copiare dati da Oracle, impostare il tipo di origine nell'attività di copia `OracleSource`su. Nella sezione **source** dell'attività di copia sono supportate le proprietà seguenti.

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà Type dell'origine dell'attività di copia deve essere impostata `OracleSource`su. | Sì |
| oracleReaderQuery | Usare la query SQL personalizzata per leggere i dati. Un esempio è `"SELECT * FROM MyTable"`.<br>Quando si Abilita il caricamento partizionato, è necessario associare tutti i parametri di partizione predefiniti corrispondenti nella query. Per esempi, vedere la sezione [copia parallela da Oracle](#parallel-copy-from-oracle) . | No |
| partitionOptions | Specifica le opzioni di partizionamento dei dati utilizzate per caricare dati da Oracle. <br>I valori consentiti sono i seguenti: **Nessuna** (impostazione predefinita), **PhysicalPartitionsOfTable** e **DynamicRange**.<br>Quando è abilitata un'opzione di partizione (ovvero non `None`), il grado di parallelismo per caricare simultaneamente i dati da un database Oracle è controllato [`parallelCopies`](copy-activity-performance.md#parallel-copy) dall'impostazione dell'attività di copia. | No |
| partitionSettings | Consente di specificare il gruppo di impostazioni per il partizionamento dei dati. <br>Applicare quando l'opzione partition non `None`è. | No |
| partitionNames | Elenco di partizioni fisiche che devono essere copiate. <br>Applicare quando l'opzione di partizione `PhysicalPartitionsOfTable`è. Se si utilizza una query per recuperare i dati di origine, `?AdfTabularPartitionName` associare la clausola WHERE. Per un esempio, vedere la sezione [copia parallela da Oracle](#parallel-copy-from-oracle) . | No |
| partitionColumnName | Specificare il nome della colonna di origine **nel tipo Integer** che verrà utilizzato dal partizionamento dell'intervallo per la copia parallela. Se non è specificato, la chiave primaria della tabella viene rilevata automaticamente e utilizzata come colonna della partizione. <br>Applicare quando l'opzione di partizione `DynamicRange`è. Se si utilizza una query per recuperare i dati di origine, `?AdfRangePartitionColumnName` associare la clausola WHERE. Per un esempio, vedere la sezione [copia parallela da Oracle](#parallel-copy-from-oracle) . | No |
| partitionUpperBound | Valore massimo della colonna di partizione in cui copiare i dati. <br>Applicare quando l'opzione di partizione `DynamicRange`è. Se si utilizza una query per recuperare i dati di origine, `?AdfRangePartitionUpbound` associare la clausola WHERE. Per un esempio, vedere la sezione [copia parallela da Oracle](#parallel-copy-from-oracle) . | No |
| partitionLowerBound | Valore minimo della colonna di partizione in cui copiare i dati. <br>Applicare quando l'opzione di partizione `DynamicRange`è. Se si utilizza una query per recuperare i dati di origine, `?AdfRangePartitionLowbound` associare la clausola WHERE. Per un esempio, vedere la sezione [copia parallela da Oracle](#parallel-copy-from-oracle) . | No |

**Esempio: copiare i dati usando una query di base senza partizione**

```json
"activities":[
    {
        "name": "CopyFromOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Oracle input dataset name>",
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
                "type": "OracleSource",
                "oracleReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="oracle-as-sink"></a>Oracle come sink

Per copiare dati in Oracle, impostare il tipo di sink nell'attività di copia `OracleSink`su. Nella sezione **sink** dell'attività di copia sono supportate le proprietà seguenti.

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà Type del sink dell'attività di copia deve essere impostata `OracleSink`su. | Yes |
| writeBatchSize | Inserisce i dati nella tabella SQL quando la dimensione del buffer `writeBatchSize`raggiunge.<br/>I valori consentiti sono integer (numero di righe). |No (il valore predefinito è 10.000) |
| writeBatchTimeout | Tempo di attesa per l'operazione di inserimento batch da completare prima del timeout.<br/>I valori consentiti sono un intervallo di tempo. Ad esempio "00:30:00" (30 minuti). | No |
| preCopyScript | Specificare una query SQL per l'attività di copia da eseguire prima di scrivere i dati in Oracle a ogni esecuzione. È possibile usare questa proprietà per pulire i dati precaricati. | No |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyToOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Oracle output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "OracleSink"
            }
        }
    }
]
```

## <a name="parallel-copy-from-oracle"></a>Copia parallela da Oracle

Il Data Factory connettore Oracle fornisce il partizionamento dei dati predefinito per copiare dati da Oracle in parallelo. È possibile trovare le opzioni di partizionamento dei dati nella scheda **origine** dell'attività di copia.

![Screenshot delle opzioni di partizione](./media/connector-oracle/connector-oracle-partition-options.png)

Quando si Abilita la copia partizionata, Data Factory esegue query parallele sull'origine Oracle per caricare i dati in base alle partizioni. Il grado parallelo è controllato dall' [`parallelCopies`](copy-activity-performance.md#parallel-copy) impostazione dell'attività di copia. Se, ad esempio, si `parallelCopies` imposta su quattro, data factory genera ed esegue contemporaneamente quattro query in base all'opzione di partizione specificata e alle impostazioni e ogni query recupera una porzione di dati dal database Oracle.

Si consiglia di abilitare la copia parallela con il partizionamento dei dati, specialmente quando si caricano grandi quantità di dati dal database Oracle. Di seguito sono elencate le configurazioni consigliate per diversi scenari. Quando si copiano dati in un archivio dati basato su file, viene riordinata la scrittura in una cartella come più file (specifica solo il nome della cartella), nel qual caso le prestazioni sono migliori rispetto alla scrittura in un singolo file.

| Scenario                                                     | Impostazioni consigliate                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Caricamento completo da tabelle di grandi dimensioni, con partizioni fisiche.          | **Opzione partizione**: Partizioni fisiche della tabella. <br><br/>Durante l'esecuzione, Data Factory rileva automaticamente le partizioni fisiche e copia i dati in base alle partizioni. |
| Caricamento completo da tabelle di grandi dimensioni, senza partizioni fisiche, mentre con una colonna di tipo integer per il partizionamento dei dati. | **Opzioni partizione**: Partizione a intervalli dinamici.<br>**Colonna partizione**: Specificare la colonna utilizzata per partizionare i dati. Se non è specificato, viene utilizzata la colonna chiave primaria. |
| Caricare una grande quantità di dati tramite una query personalizzata con partizioni fisiche. | **Opzione partizione**: Partizioni fisiche della tabella.<br>**Query**: `SELECT * FROM <TABLENAME> PARTITION("?AdfTabularPartitionName") WHERE <your_additional_where_clause>`.<br>**Nome partizione**: Specificare i nomi di partizione da cui copiare i dati. Se non specificato, Data Factory rileva automaticamente le partizioni fisiche nella tabella specificata nel set di dati Oracle.<br><br>Durante l'esecuzione, data factory `?AdfTabularPartitionName` sostituisce con il nome effettivo della partizione e invia a Oracle. |
| Caricare una grande quantità di dati tramite una query personalizzata, senza partizioni fisiche, mentre con una colonna integer per il partizionamento dei dati. | **Opzioni partizione**: Partizione a intervalli dinamici.<br>**Query**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`.<br>**Colonna partizione**: Specificare la colonna utilizzata per partizionare i dati. È possibile partizionare la colonna con il tipo di dati Integer.<br>Limite **superiore della partizione** e **limite inferiore della partizione**: Specificare se si desidera filtrare in base alla colonna di partizione per recuperare i dati solo tra l'intervallo inferiore e quello superiore.<br><br>Durante l'esecuzione, data factory `?AdfRangePartitionColumnName`sostituisce `?AdfRangePartitionUpbound`, e `?AdfRangePartitionLowbound` con il nome della colonna e gli intervalli di valori effettivi per ogni partizione e invia a Oracle. <br>Se ad esempio la colonna di partizione "ID" è impostata con il limite inferiore 1 e il limite superiore come 80, con la copia parallela impostata su 4, Data Factory recupera i dati di 4 partizioni. I rispettivi ID sono rispettivamente compresi tra [1, 20], [21, 40], [41, 60] e [61, 80]. |

**Esempio: query con partizione fisica**

```json
"source": {
    "type": "OracleSource",
    "query": "SELECT * FROM <TABLENAME> PARTITION(\"?AdfTabularPartitionName\") WHERE <your_additional_where_clause>",
    "partitionOption": "PhysicalPartitionsOfTable",
    "partitionSettings": {
        "partitionNames": [
            "<partitionA_name>",
            "<partitionB_name>"
        ]
    }
}
```

**Esempio: query con partizione a intervalli dinamici**

```json
"source": {
    "type": "OracleSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="data-type-mapping-for-oracle"></a>Mapping dei tipi di dati per Oracle

Quando si copiano dati da e in Oracle, vengono applicati i mapping seguenti. Per informazioni su come l'attività di copia esegue il mapping dello schema di origine e del tipo di dati al sink, vedere [Mapping dello schema e del tipo di dati](copy-activity-schema-and-type-mapping.md).

| Tipo di dati Oracle | Tipo di dati provvisorio di Data Factory |
|:--- |:--- |
| BFILE |Byte[] |
| BLOB |Byte[]<br/>(supportato solo in Oracle 10g e versioni successive) |
| CHAR |String |
| CLOB |String |
| DATE |DateTime |
| FLOAT |Decimal, String (se la precisione > 28) |
| INTEGER |Decimal, String (se la precisione > 28) |
| LONG |String |
| LONG RAW |Byte[] |
| NCHAR |String |
| NCLOB |String |
| NUMBER |Decimal, String (se la precisione > 28) |
| NVARCHAR2 |String |
| RAW |Byte[] |
| ROWID |String |
| TIMESTAMP |DateTime |
| TIMESTAMP WITH LOCAL TIME ZONE |String |
| TIMESTAMP WITH TIME ZONE |String |
| UNSIGNED INTEGER |NUMBER |
| VARCHAR2 |String |
| XML |String |

> [!NOTE]
> I tipi di dati INTERVAL YEAR TO MONTH e INTERVAL DAY TO SECOND non sono supportati.


## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini e sink dall'attività di copia in Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md##supported-data-stores-and-formats).

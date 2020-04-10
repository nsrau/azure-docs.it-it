---
title: Copiare dati da e verso Oracle usando Azure Data FactoryCopy data to and from Oracle by using Azure Data Factory
description: Informazioni su come copiare i dati dagli archivi di origine supportati in un database Oracle o da Oracle agli archivi sink supportati tramite Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: jingwang
ms.openlocfilehash: 534e5c913685eeac92022f6694ea31b24816da5d
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011651"
---
# <a name="copy-data-from-and-to-oracle-by-using-azure-data-factory"></a>Copiare dati da e in Oracle usando Azure Data Factory
> [!div class="op_single_selector" title1="Selezionare uSelezionare la versione del servizio di Azure Data Factory in uso:"]
> * [Versione 1](v1/data-factory-onprem-oracle-connector.md)
> * [Versione corrente](connector-oracle.md)

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da e in un database Oracle.This article outlines how to use the copy activity in Azure Data Factory to copy data from and to an Oracle database. Si basa sulla [panoramica dell'attività](copy-activity-overview.md)di copia.

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo connettore Oracle è supportato per le attività seguenti:This Oracle connector is supported for the following activities:

- [Attività di copia](copy-activity-overview.md) con [matrice di origine/sink supportata](copy-activity-overview.md)
- [Attività di ricerca](control-flow-lookup-activity.md)

È possibile copiare dati da un database Oracle in qualsiasi archivio dati di sink supportato. È anche possibile copiare dati da qualsiasi archivio di dati di origine supportato in un database Oracle. Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia, vedere la tabella [Archivi dati supportati.](copy-activity-overview.md#supported-data-stores-and-formats)

In particolare, questo connettore Oracle supporta:Specifically, this Oracle connector supports:

- Le seguenti versioni di un database Oracle:
    - Oracle 19c R1 (19.1) e versioni successive
    - Oracle 18c R1 (18.1) e versioni successive
    - Oracle 12c R1 (12.1) e versioni successive
    - Oracle 11g R1 (11.1) e versioni successive
    - Oracle 10g R1 (10.1) e versioni successive
    - Oracle 9i R2 (9.2) e versioni successive
    - Oracle 8i R3 (8.1.7) e versioni successive
    - Servizio Exadata del cloud di database Oracle
- Copia parallela da un'origine Oracle.Parallel copying from an Oracle source. Vedere la sezione [Copia parallela da Oracle](#parallel-copy-from-oracle) per i dettagli.

> [!Note]
> Il server proxy Oracle non è supportato.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)] 

Il runtime di integrazione fornisce un driver Oracle incorporato. Non è pertanto necessario installare manualmente un driver quando si copiano dati da e in Oracle.

## <a name="get-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà usate per definire entità di Data Factory specifiche per il connettore Oracle.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Il servizio collegato Oracle supporta le seguenti proprietà:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su **Oracle**. | Sì |
| connectionString | Specifica le informazioni necessarie per la connessione all'istanza del database Oracle. <br/>È anche possibile inserire una password nell'insieme di credenziali delle chiavi di Azure ed estrarre la `password` configurazione dalla stringa di connessione. Fare riferimento agli esempi seguenti e [archiviare le credenziali in Archiviazione](store-credentials-in-key-vault.md) dell'insieme di credenziali delle chiavi di Azure con altri dettagli. <br><br>**Tipo di connessione supportato**: è possibile usare l'**ID di sicurezza Oracle** o il **nome del servizio Oracle** per identificare il database:<br>- Se si usa il SID: `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;`<br>- Se si usa il nome del servizio: `Host=<host>;Port=<port>;ServiceName=<servicename>;User Id=<username>;Password=<password>;`<br>Per le opzioni di connessione native Oracle avanzate, è possibile scegliere di aggiungere una voce in TNSNAMES.For advanced Oracle native connection options, you can choose to add an entry in [TNSNAMES. ORA](http://www.orafaq.com/wiki/Tnsnames.ora) nel server Oracle e nel servizio collegato ADF Oracle scegliere di utilizzare il tipo di connessione Nome servizio Oracle e configurare il nome del servizio corrispondente. | Sì |
| connectVia | Runtime [di integrazione](concepts-integration-runtime.md) da utilizzare per la connessione all'archivio dati. Per ulteriori informazioni, vedere la sezione [Prerequisiti.](#prerequisites) Se questa proprietà non è specificata, viene usato il tipo Azure Integration Runtime predefinito. |No |

>[!TIP]
>Se viene visualizzato un errore, "ORA-01025: parametro UPI non compreso `WireProtocolMode=1` nell'intervallo" e la versione Oracle è 8i, aggiungere alla stringa di connessione. Quindi riprovare.

Altre proprietà di connessione che è possibile impostare nella stringa di connessione in base al caso:More connection properties you can set in connection string per your case:

| Proprietà | Descrizione | Valori consentiti |
|:--- |:--- |:--- |
| ArraySize |Numero di byte che il connettore può recuperare in un singolo round trip di rete. Ad esempio, `ArraySize=‭10485760‬`.<br/><br/>Valori maggiori aumentano la velocità effettiva riducendo il numero di volte per recuperare i dati in rete. Valori più piccoli aumentano il tempo di risposta, poiché è meno in attesa del server per la trasmissione dei dati. | Numero intero compreso tra 1 e 4294967296 (4 GB). Il valore predefinito è `60000`. Il valore 1 non definisce il numero di byte, ma indica l'allocazione di spazio per esattamente una riga di dati. |

Per abilitare la crittografia sulla connessione di Oracle, sono disponibili due opzioni:

-   Per utilizzare **Triple-DES Encryption (3DES) e Advanced Encryption Standard (AES)**, sul lato server Oracle, accedere a Oracle Advanced Security (OAS) e configurare le impostazioni di crittografia. Per informazioni dettagliate, vedere la [documentazione](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759)di Oracle . Il connettore Oracle Application Development Framework (ADF) negozia automaticamente il metodo di crittografia per utilizzare quello configurato in OAS quando si stabilisce una connessione a Oracle.

-   Per utilizzare **TLS**:

    1.  Ottenere le informazioni sul certificato TLS/SSL. Ottenere le informazioni sul certificato con codifica DER (Distinguished Encoding Rules) del certificato TLS/SSL e salvare l'output (----- Inizia certificato ... End Certificate -----) come file di testo.

        ```
        openssl x509 -inform DER -in [Full Path to the DER Certificate including the name of the DER Certificate] -text
        ```

        **Esempio:** Estrarre le informazioni del certificato da DeRcert.cer, quindi salvare l'output in cert.txt.

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
    
    2.  Compilare `keystore` `truststore`il file o . Il comando seguente `truststore` crea il file, con o senza password, in formato PKCS-12.

        ```
        openssl pkcs12 -in [Path to the file created in the previous step] -out [Path and name of TrustStore] -passout pass:[Keystore PWD] -nokeys -export
        ```

        **Esempio:** Creare un file `truststore` PKCS12, denominato MyTrustStoreFile, con una password.

        ```
        openssl pkcs12 -in cert.txt -out MyTrustStoreFile -passout pass:ThePWD -nokeys -export  
        ```

    3.  Posizionare `truststore` il file sul computer a smacchinari auto-ospitato. Ad esempio, inserire il file in C:.
    4.  In Azure Data Factory configurare `EncryptionMethod=1` la stringa `TrustStore` / `TrustStorePassword`di connessione Oracle con e il valore corrispondente. Ad esempio: `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;EncryptionMethod=1;TrustStore=C:\\MyTrustStoreFile;TrustStorePassword=<trust_store_password>`.

**Esempio:**

```json
{
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;"
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
            "connectionString": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;",
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

Questa sezione presenta un elenco delle proprietà supportate dal set di dati Oracle. Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei dataset, vedere [Dataset](concepts-datasets-linked-services.md). 

Per copiare dati da e in Oracle, `OracleTable`impostare la proprietà type del dataset su . Sono supportate le proprietà seguenti.

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del dataset `OracleTable`deve essere impostata su . | Sì |
| schema | Nome dello schema. |No per l'origine, Sì per il sink  |
| tabella | Nome della tabella/vista. |No per l'origine, Sì per il sink  |
| tableName | Nome della tabella/vista con schema. Questa proprietà è supportata per compatibilità con le versioni precedenti. Per un nuovo `schema` `table`carico di lavoro, utilizzare e . | No per l'origine, Sì per il sink |

**Esempio:**

```json
{
    "name": "OracleDataset",
    "properties":
    {
        "type": "OracleTable",
        "schema": [],
        "typeProperties": {
            "schema": "<schema_name>",
            "table": "<table_name>"
        },
        "linkedServiceName": {
            "referenceName": "<Oracle linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Questa sezione presenta un elenco delle proprietà supportate dall'origine e dal sink Oracle. Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). 

### <a name="oracle-as-source"></a>Oracle come origine

>[!TIP]
>Per caricare i dati da Oracle in modo efficiente utilizzando il partizionamento dei dati, vedere [Copia parallela da Oracle](#parallel-copy-from-oracle).

Per copiare dati da Oracle, impostare `OracleSource`il tipo di origine nell'attività di copia su . Le proprietà seguenti sono supportate nella sezione **relativa all'origine** dell'attività di copia.

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type dell'origine dell'attività di copia deve essere impostata su `OracleSource`. | Sì |
| oracleReaderQuery | Usare la query SQL personalizzata per leggere i dati. Un esempio è `"SELECT * FROM MyTable"`.<br>Quando si abilita il carico partizionato, è necessario associare tutti i parametri di partizione incorporati corrispondenti nella query. Per esempi, vedere la sezione [Copia parallela da Oracle.For](#parallel-copy-from-oracle) examples, see the Parallel copy from Oracle section. | No |
| partizioniOpzioni | Specifica le opzioni di partizionamento dei dati utilizzate per caricare i dati da Oracle. <br>I valori consentiti sono: **None (impostazione** predefinita), **PhysicalPartitionsOfTable** e **DynamicRange**.<br>Quando un'opzione di partizione `None`è abilitata ( ovvero non ), il grado di parallelismo per il caricamento simultaneo dei dati da un database Oracle è controllato dall'impostazione [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) dell'attività di copia. | No |
| partitionImpostazioni | Specificare il gruppo di impostazioni per il partizionamento dei dati. <br>Applicare quando l'opzione `None`di partizione non è . | No |
| partitionNames (nomi di partizione) | Elenco di partizioni fisiche da copiare. <br>Applicare quando l'opzione di partizione è `PhysicalPartitionsOfTable`. Se si utilizza una query per `?AdfTabularPartitionName` recuperare i dati di origine, eseguire l'hook nella clausola WHERE. Per un esempio, vedere la sezione [Copia parallela da Oracle.For](#parallel-copy-from-oracle) an example, see the Parallel copy from Oracle section. | No |
| partitionColumnName (nome di colonna) | Specificare il nome della colonna di origine **in tipo Integer** che verrà utilizzato dal partizionamento dell'intervallo per la copia parallela. Se non specificato, la chiave primaria della tabella viene rilevata automaticamente e utilizzata come colonna di partizione. <br>Applicare quando l'opzione di partizione è `DynamicRange`. Se si utilizza una query per `?AdfRangePartitionColumnName` recuperare i dati di origine, eseguire l'hook nella clausola WHERE. Per un esempio, vedere la sezione [Copia parallela da Oracle.For](#parallel-copy-from-oracle) an example, see the Parallel copy from Oracle section. | No |
| partitionUpperBound | Valore massimo della colonna della partizione in cui copiare i dati. <br>Applicare quando l'opzione di partizione è `DynamicRange`. Se si utilizza una query per `?AdfRangePartitionUpbound` recuperare i dati di origine, eseguire l'hook nella clausola WHERE. Per un esempio, vedere la sezione [Copia parallela da Oracle.For](#parallel-copy-from-oracle) an example, see the Parallel copy from Oracle section. | No |
| partizioneLowerBound | Valore minimo della colonna della partizione in cui copiare i dati. <br>Applicare quando l'opzione di partizione è `DynamicRange`. Se si utilizza una query per `?AdfRangePartitionLowbound` recuperare i dati di origine, eseguire l'hook nella clausola WHERE. Per un esempio, vedere la sezione [Copia parallela da Oracle.For](#parallel-copy-from-oracle) an example, see the Parallel copy from Oracle section. | No |

**Esempio: copiare i dati utilizzando una query di base senza partizioneExample: copy data by using a basic query without partition**

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

Per copiare i dati in Oracle, impostare il tipo di sink nell'attività di copia su `OracleSink`. Le proprietà seguenti sono supportate nella sezione del **sink** dell'attività di copia.

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del sink dell'attività di copia deve essere impostata su `OracleSink`. | Sì |
| writeBatchSize | Inserisce dati nella tabella SQL quando `writeBatchSize`la dimensione del buffer raggiunge .<br/>I valori consentiti sono integer (numero di righe). |No (il valore predefinito è 10.000) |
| writeBatchTimeout | Tempo di attesa per l'operazione di inserimento batch da completare prima del timeout.<br/>I valori consentiti sono un intervallo di tempo. Ad esempio "00:30:00" (30 minuti). | No |
| preCopyScript | Specificare una query SQL per l'attività di copia da eseguire prima di scrivere i dati in Oracle in ogni esecuzione. È possibile usare questa proprietà per pulire i dati precaricati. | No |

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

Il connettore Data Factory Oracle fornisce il partizionamento dei dati incorporato per copiare i dati da Oracle in parallelo. Le opzioni di partizionamento dei dati sono disponibili nella scheda **Origine** dell'attività di copia.

![Screenshot delle opzioni di partizione](./media/connector-oracle/connector-oracle-partition-options.png)

Quando si abilita la copia partizionata, Data Factory esegue query parallele sull'origine Oracle per caricare i dati in base alle partizioni. Il grado parallelo è [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) controllato dall'impostazione dell'attività di copia. Ad esempio, se `parallelCopies` si imposta su quattro, Data Factory genera ed esegue contemporaneamente quattro query in base all'opzione e alle impostazioni di partizione specificate e ogni query recupera una parte di dati dal database Oracle.

Si consiglia di abilitare la copia parallela con il partizionamento dei dati, soprattutto quando si carica grandi quantità di dati dal database Oracle. Di seguito sono riportate le configurazioni consigliate per scenari diversi. Quando si copiano dati nell'archivio dati basato su file, viene comandato di scrivere in una cartella come più file (specificare solo il nome della cartella), nel qual caso le prestazioni sono migliori rispetto alla scrittura in un singolo file.

| Scenario                                                     | Impostazioni suggerite                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Carico completo da tabelle di grandi dimensioni, con partizioni fisiche.          | **Opzione partizione**: Partizioni fisiche della tabella. <br><br/>Durante l'esecuzione, Data Factory rileva automaticamente le partizioni fisiche e copia i dati in base alle partizioni. |
| Carico completo da tabella di grandi dimensioni, senza partizioni fisiche, mentre con una colonna intera per il partizionamento dei dati. | **Opzioni di partizione**: Partizione a intervallo dinamico.<br>**Colonna partizione**: Specificare la colonna utilizzata per partizionare i dati. Se non specificato, viene utilizzata la colonna di chiave primaria. |
| Caricare una grande quantità di dati utilizzando una query personalizzata, con partizioni fisiche. | **Opzione partizione**: Partizioni fisiche della tabella.<br>**Query** `SELECT * FROM <TABLENAME> PARTITION("?AdfTabularPartitionName") WHERE <your_additional_where_clause>`: .<br>**Nome partizione**: Specificare i nomi delle partizioni da cui copiare i dati. Se non specificato, Data Factory rileva automaticamente le partizioni fisiche nella tabella specificata nel set di dati Oracle.<br><br>Durante l'esecuzione, `?AdfTabularPartitionName` Data Factory sostituisce con il nome effettivo della partizione e viene inviato a Oracle.During execution, Data Factory replaces with the actual partition name, and sends to Oracle. |
| Caricare una grande quantità di dati utilizzando una query personalizzata, senza partizioni fisiche, mentre con una colonna intera per il partizionamento dei dati. | **Opzioni di partizione**: Partizione a intervallo dinamico.<br>**Query** `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`: .<br>**Colonna partizione**: Specificare la colonna utilizzata per partizionare i dati. È possibile partizionare in base alla colonna con tipo di dati integer.<br>**Partizione limite superiore** e **limite inferiore della partizione:** specificare se si desidera filtrare in base alla colonna della partizione per recuperare i dati solo tra l'intervallo inferiore e superiore.<br><br>Durante l'esecuzione, `?AdfRangePartitionColumnName`Data `?AdfRangePartitionUpbound`Factory `?AdfRangePartitionLowbound` sostituisce , e con il nome di colonna e gli intervalli di valori effettivi per ogni partizione e invia a Oracle. <br>Ad esempio, se la colonna della partizione "ID" è impostata con il limite inferiore come 1 e il limite superiore come 80, con la copia parallela impostata come 4, Data Factory recupera i dati da 4 partizioni. I loro ID sono compresi tra [1,20], [21, 40], [41, 60] e [61, 80], rispettivamente. |

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

**Esempio: query con partizione di intervallo dinamicoExample: query with dynamic range partition**

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
| CHAR |string |
| CLOB |string |
| DATE |Datetime |
| FLOAT |Decimal, String (se la precisione > 28) |
| INTEGER |Decimal, String (se la precisione > 28) |
| LONG |string |
| LONG RAW |Byte[] |
| NCHAR |string |
| NCLOB |string |
| NUMBER |Decimal, String (se la precisione > 28) |
| NVARCHAR2 |string |
| RAW |Byte[] |
| ROWID |string |
| timestamp |Datetime |
| TIMESTAMP WITH LOCAL TIME ZONE |string |
| TIMESTAMP WITH TIME ZONE |string |
| UNSIGNED INTEGER |Number |
| VARCHAR2 |string |
| XML |string |

> [!NOTE]
> I tipi di dati INTERVAL YEAR TO MONTH e INTERVAL DAY TO SECOND non sono supportati.

## <a name="lookup-activity-properties"></a>Proprietà dell'attività di ricerca

Per informazioni dettagliate sulle proprietà, selezionare [Attività di ricerca](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini e sink dall'attività di copia in Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

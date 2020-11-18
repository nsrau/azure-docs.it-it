---
title: Copiare dati da Hive usando Azure Data Factory
description: Informazioni su come copiare dati da Hive in archivi dati sink supportati usando un'attività di copia in una pipeline di Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: jingwang
ms.openlocfilehash: 4207c4ddfcbab325b1ae119dcd200af30fc59f58
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844957"
---
# <a name="copy-and-transform-data-from-hive-using-azure-data-factory"></a>Copiare e trasformare i dati da hive usando Azure Data Factory 
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da Hive. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale sull'attività di copia.

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo connettore hive è supportato per le attività seguenti:

- [Attività Copy](copy-activity-overview.md) con [matrice di origine/sink supportata](copy-activity-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)

È possibile copiare dati da Hive a qualsiasi archivio dati sink supportato. Per un elenco degli archivi dati supportati come origini/sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory offre un driver predefinito per consentire la connettività, pertanto non è necessario installare manualmente alcun driver usando questo connettore.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire le entità di Data Factory specifiche per il connettore Hive.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato Hive sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su: **Hive** | Sì |
| host | Indirizzo IP o nome host del server hive, separato da ";" per più host (solo quando serviceDiscoveryMode è abilitato).  | Sì |
| port | Porta TCP che il server Hive usa per l'ascolto delle connessioni client. Se ci si connette a Azure HDInsights, specificare la porta come 443. | Sì |
| serverType | Tipo di server Hive. <br/>I valori consentiti sono: **HiveServer1**, **HiveServer2**, **HiveThriftServer** | No |
| thriftTransportProtocol | Protocollo di trasporto da usare nel livello Thrift. <br/>I valori consentiti sono **Binary**, **SASL**, **HTTP** | No |
| authenticationType | Metodo di autenticazione usato per accedere al server Hive. <br/>I valori consentiti sono: **Anonymous**, **username**, **UsernameAndPassword**, **WindowsAzureHDInsightService**. L'autenticazione Kerberos non è attualmente supportata. | Sì |
| serviceDiscoveryMode | true per indicare l'uso del servizio ZooKeeper; in caso contrario, false.  | No |
| zooKeeperNameSpace | Spazio dei nomi in ZooKeeper nel quale vengono aggiunti i nodi del server Hive 2.  | No |
| useNativeQuery | Specifica se il driver usa query HiveQL native o li converte in un formato equivalente in HiveQL.  | No |
| username | Nome utente usato per accedere al server Hive.  | No |
| password | Password corrispondente all'utente. Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Azure Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). | No |
| httpPath | URL parziale corrispondente al server Hive.  | No |
| enableSsl | Specifica se le connessioni al server sono crittografate con TLS. Il valore predefinito è false.  | No |
| trustedCertPath | Percorso completo del file con estensione PEM contenente i certificati CA attendibili per la verifica del server durante la connessione tramite TLS. Questa proprietà può essere impostata solo quando si usa TLS sul runtime di integrazione self-hosted. Il valore predefinito è il file cacerts.pem installato con il runtime di integrazione.  | No |
| useSystemTrustStore | Specifica se usare o meno un certificato della CA dall'archivio di scopi consentiti o da un file .pem specificato. Il valore predefinito è false.  | No |
| allowHostNameCNMismatch | Specifica se è necessario che il nome del certificato TLS/SSL emesso dall'autorità di certificazione corrisponda al nome host del server durante la connessione tramite TLS. Il valore predefinito è false.  | No |
| allowSelfSignedServerCert | Specifica se consentire o meno i certificati autofirmati dal server. Il valore predefinito è false.  | No |
| connectVia | Il [runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. Per altre informazioni, vedere la sezione [Prerequisiti](#prerequisites). Se non specificato, viene usato il runtime di integrazione di Azure predefinito. |No |
| storageReference | Riferimento al servizio collegato dell'account di archiviazione usato per la gestione temporanea dei dati nel flusso di dati di mapping. Questa operazione è necessaria solo quando si usa il servizio collegato hive nel flusso di dati di mapping | No |

**Esempio:**

```json
{
    "name": "HiveLinkedService",
    "properties": {
        "type": "Hive",
        "typeProperties": {
            "host" : "<cluster>.azurehdinsight.net",
            "port" : "<port>",
            "authenticationType" : "WindowsAzureHDInsightService",
            "username" : "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sui [set di dati](concepts-datasets-linked-services.md). Questa sezione presenta un elenco delle proprietà supportate dal set di dati Hive.

Per copiare dati da Hive, impostare la proprietà type del set di dati su **HiveObject**. Sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà Type del set di dati deve essere impostata su: **HiveObject** | Sì |
| schema | Nome dello schema. |No (se nell'origine dell'attività è specificato "query")  |
| tabella | Nome della tabella. |No (se nell'origine dell'attività è specificato "query")  |
| tableName | Nome della tabella che include la parte dello schema. Questa proprietà è supportata per garantire la compatibilità con le versioni precedenti. Per i nuovi carichi di lavoro, usare `schema` e `table`. | No (se nell'origine dell'attività è specificato "query") |

**Esempio**

```json
{
    "name": "HiveDataset",
    "properties": {
        "type": "HiveObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Hive linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine Hive.

### <a name="hivesource-as-source"></a>HiveSource come origine

Per copiare dati da Hive, impostare il tipo di origine nell'attività di copia su **HiveSource**. Nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type dell'origine dell'attività di copia deve essere impostata su **HiveSource** | Sì |
| query | Usare la query SQL personalizzata per leggere i dati. Ad esempio: `"SELECT * FROM MyTable"`. | No (se nel set di dati è specificato "tableName") |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromHive",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Hive input dataset name>",
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
                "type": "HiveSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="mapping-data-flow-properties"></a>Proprietà del flusso di dati per mapping

Il connettore hive è supportato come origine del set di dati in [linea](data-flow-source.md#inline-datasets) nei flussi di dati di mapping. Leggere usando una query o direttamente da una tabella hive in HDInsight. I dati hive vengono gestiti in modo temporaneo in un account di archiviazione come file parquet prima di essere trasformati come parte di un flusso di dati. 

### <a name="source-properties"></a>Proprietà origine

La tabella seguente elenca le proprietà supportate da un'origine hive. È possibile modificare queste proprietà nella scheda **Opzioni di origine** .

| Nome | Descrizione | Obbligatoria | Valori consentiti | Proprietà script flusso di dati |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Archivio | L'archivio deve essere `hive` | sì |  `hive` | store | 
| Formato | Se si esegue la lettura da una tabella o da una query | sì | `table` o `query` | format |
| Nome schema | Se si esegue la lettura da una tabella, lo schema della tabella di origine |  Sì, se format è `table` | string | schemaName |
| Nome tabella | Se si esegue la lettura da una tabella, il nome della tabella |   Sì, se format è `table` | string | tableName |
| Query | Se format è `query` , la query di origine sul servizio collegato hive | Sì, se format è `query` | string | query |
| Gestione temporanea | La tabella hive verrà sempre preparata per la gestione temporanea. | sì | `true` | gestione temporanea |
| Contenitore di archiviazione | Contenitore di archiviazione usato per la gestione temporanea dei dati prima della lettura da hive o scrittura in hive. Il cluster hive deve avere accesso a questo contenitore. | sì | string | storageContainer |
| Database di gestione temporanea | Schema/database in cui l'account utente specificato nel servizio collegato può accedere a. Viene usato per creare tabelle esterne durante la gestione temporanea e rilasciate in seguito | No | `true` o `false` | stagingDatabaseName |
| Script pre-SQL | Codice SQL da eseguire nella tabella hive prima di leggere i dati | No | string | preSQLs |

#### <a name="source-example"></a>Esempio di origine

Di seguito è riportato un esempio di configurazione dell'origine hive:

![Esempio di origine hive](media/data-flow/hive-source.png "[Esempio di origine hive")

Queste impostazioni vengono convertite nello script del flusso di dati seguente:

```
source(
    allowSchemaDrift: true,
    validateSchema: false,
    ignoreNoFilesFound: false,
    format: 'table',
    store: 'hive',
    schemaName: 'default',
    tableName: 'hivesampletable',
    staged: true,
    storageContainer: 'khive',
    storageFolderPath: '',
    stagingDatabaseName: 'default') ~> hivesource
```
### <a name="known-limitations"></a>Limitazioni note

* Tipi complessi quali matrici, mappe, struct e unioni non sono supportati per la lettura. 
* Il connettore hive supporta solo le tabelle hive in Azure HDInsight della versione 4,0 o successive (Apache Hive 3.1.0)

## <a name="lookup-activity-properties"></a>Proprietà dell'attività Lookup

Per altre informazioni sulle proprietà, vedere [Attività Lookup](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

---
title: Copiare dati da SAP HANA
description: Informazioni su come copiare dati da SAP HANA in archivi dati di sink supportati usando un'attività di copia in una pipeline di Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/17/2020
ms.openlocfilehash: fa165c21622110bb18476efdebf3264a11e26ad7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265883"
---
# <a name="copy-data-from-sap-hana-using-azure-data-factory"></a>Copiare dati da SAP HANA usando Azure Data Factory
> [!div class="op_single_selector" title1="Selezionare uSelezionare la versione del servizio di Azure Data Factory in uso:"]
> * [Versione 1](v1/data-factory-sap-hana-connector.md)
> * [Versione corrente](connector-sap-hana.md)

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da un database SAP HANA. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale sull'attività di copia.

>[!TIP]
>Per informazioni sul supporto generale di ADF sullo scenario di integrazione dei dati SAP, vedere il [white paper sull'integrazione](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) dei dati SAP con introduzione, comparsa e indicazioni dettagliate.

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo connettore SAP HANA è supportato per le attività seguenti:This SAP HANA connector is supported for the following activities:

- [Attività di copia](copy-activity-overview.md) con [matrice di origine/sink supportata](copy-activity-overview.md)
- [Attività di ricerca](control-flow-lookup-activity.md)

È possibile copiare dati da un database SAP HANA in qualsiasi archivio dati di sink supportato. Per un elenco degli archivi dati supportati come origini/sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

In particolare, il connettore SAP HANA supporta:

- La copia di dati da qualsiasi versione del database SAP HANA.
- Copia dei dati da **modelli informativi HANA** (ad esempio viste analitiche e di calcolo) e **tabelle riga/colonna**.
- La copia di dati usando l'autenticazione **Di base** o **Windows**.
- Copia parallela da un'origine SAP HANA. Per informazioni dettagliate, vedere la sezione [Copia parallela da SAP HANA.](#parallel-copy-from-sap-hana)

> [!TIP]
> Per copiare dati **in** un archivio dati SAP HANA, usare il connettore ODBC generico. Per i dettagli, vedere [Sink SAP HANA](connector-odbc.md#sap-hana-sink). Si noti che i servizi collegati per i connettori SAP HANA e ODBC sono associati a tipi diversi e pertanto non possono essere riusati.

## <a name="prerequisites"></a>Prerequisiti

Per usare questo connettore SAP HANA, è necessario:

- Configurare un runtime di integrazione self-hosted. Per informazioni dettagliate, vedere l'articolo [Self-hosted Integration Runtime.See Self-hosted Integration Runtime](create-self-hosted-integration-runtime.md) article for details.
- Installare il driver ODBC di SAP HANA nel computer del runtime di integrazione. È possibile scaricare il driver ODBC di SAP HANA dall'[area per il download di software SAP](https://support.sap.com/swdc). Per cercare il driver, usare la parola chiave **SAP HANA CLIENT for Windows**.

## <a name="getting-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire entità di Data Factory specifiche per il connettore SAP HANA.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato di SAP HANA sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su: **SapHana** | Sì |
| connectionString | Specificare le informazioni necessarie per connettersi a SAP HANA utilizzando **l'autenticazione di base** o **l'autenticazione**di Windows. Vedere gli esempi seguenti.<br>Nella stringa di connessione, server/porta è obbligatorio (la porta predefinita è 30015) e nome utente e password sono obbligatori quando si utilizza l'autenticazione di base. Per ulteriori impostazioni avanzate, fare riferimento a [Proprietà di connessione SAP HANA ODBCFor additional advanced settings,](<https://help.sap.com/viewer/0eec0d68141541d1b07893a39944924e/2.0.02/en-US/7cab593774474f2f8db335710b2f5c50.html>) refer to SAP HANA ODBC Connection Properties<br/>È anche possibile inserire la password nell'insieme di credenziali delle chiavi di Azure ed estrarre la configurazione della password dalla stringa di connessione. Fare riferimento all'articolo [Archiviare le credenziali nell'insieme](store-credentials-in-key-vault.md) di credenziali delle chiavi di Azure con altri dettagli. | Sì |
| userName | Specificare il nome utente quando si utilizza l'autenticazione di Windows.Specify user name when using Windows authentication. Esempio: `user@domain.com` | No |
| password | Specifica la password per l'account utente. Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). | No |
| connectVia | Il [runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È necessario un runtime di integrazione self-hosted come indicato in [Prerequisiti](#prerequisites). |Sì |

**Esempio: utilizzare l'autenticazione di baseExample: use basic authentication**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "connectionString": "SERVERNODE=<server>:<port (optional)>;UID=<userName>;PWD=<Password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Esempio: utilizzare l'autenticazione di WindowsExample: use Windows authentication**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "connectionString": "SERVERNODE=<server>:<port (optional)>;",
            "userName": "<username>", 
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

Se si utilizzava il servizio collegato SAP HANA con il payload seguente, è ancora supportato così com'è, mentre si consiglia di utilizzare quello nuovo in futuro.

**Esempio:**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "server": "<server>:<port (optional)>",
            "authenticationType": "Basic",
            "userName": "<username>",
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

## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sui [set di dati](concepts-datasets-linked-services.md). Questa sezione presenta un elenco delle proprietà supportate dal set di dati SAP HANA.

Per copiare i dati da SAP HANA, sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del set di dati deve essere impostata su: **SapHanaTable** | Sì |
| schema | Nome dello schema nel database SAP HANA. | No (se nell'origine dell'attività è specificato "query") |
| tabella | Nome della tabella nel database SAP HANA. | No (se nell'origine dell'attività è specificato "query") |

**Esempio:**

```json
{
    "name": "SAPHANADataset",
    "properties": {
        "type": "SapHanaTable",
        "typeProperties": {
            "schema": "<schema name>",
            "table": "<table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP HANA linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Se si `RelationalTable` utilizza un set di dati tipizzato, è comunque supportato così com'è, mentre viene suggerito di usare quello nuovo in futuro.

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine SAP HANA.

### <a name="sap-hana-as-source"></a>SAP HANA come origine

>[!TIP]
>Per l'inserimento efficiente dei dati da SAP HANA tramite il partizionamento dei dati, vedere la sezione [Copia parallela da SAP HANA.](#parallel-copy-from-sap-hana)

Per copiare i dati da SAP HANA, nella sezione **dell'origine dell'attività** di copia sono supportate le proprietà seguenti:To copy data from SAP HANA, the following properties are supported in the copy activity source section:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type dell'origine dell'attività di copia deve essere impostata su: **SapHanaSource** | Sì |
| query | Specifica la query SQL che consente di leggere i dati dall'istanza di SAP HANA. | Sì |
| partizioniOpzioni | Specifica le opzioni di partizionamento dei dati utilizzate per l'inserimento di dati da SAP HANA. Per ulteriori informazioni, vedere la sezione [Copia parallela da SAP HANA.](#parallel-copy-from-sap-hana)<br>I valori consentiti sono: **None (impostazione** predefinita), **PhysicalPartitionsOfTable**, **SapHanaDynamicRange**. Per ulteriori informazioni, vedere la sezione [Copia parallela da SAP HANA.](#parallel-copy-from-sap-hana) `PhysicalPartitionsOfTable`può essere utilizzato solo quando si copiano dati da una tabella ma non da una query. <br>Quando un'opzione di partizione `None`è abilitata ( ovvero non ), il grado di parallelismo per il caricamento simultaneo dei dati da SAP HANA è controllato dall'impostazione [`parallelCopies`](copy-activity-performance.md#parallel-copy) dell'attività di copia. | False |
| partitionImpostazioni | Specificare il gruppo di impostazioni per il partizionamento dei dati.<br>Applicare quando l'opzione partizione è `SapHanaDynamicRange`. | False |
| partitionColumnName (nome di colonna) | Specificare il nome della colonna di origine che verrà utilizzata dalla partizione per la copia parallela. Se non specificato, l'indice o la chiave primaria della tabella viene rilevato automaticamente e utilizzato come colonna di partizione.<br>Applicare quando l'opzione di partizione è `SapHanaDynamicRange`. Se si utilizza una query per `?AdfHanaDynamicRangePartitionCondition` recuperare i dati di origine, eseguire l'hook nella clausola WHERE. Vedere l'esempio nella sezione [Copia parallela da SAP HANA.](#parallel-copy-from-sap-hana) | Sì quando `SapHanaDynamicRange` si utilizza la partizione. |
| packetSize | Specifica la dimensione del pacchetto di rete (in Kilobyte) per dividere i dati in più blocchi. Se si dispone di una grande quantità di dati da copiare, l'aumento delle dimensioni dei pacchetti può aumentare la velocità di lettura da SAP HANA nella maggior parte dei casi. Quando si regolano le dimensioni del pacchetto, è consigliabile eseguire il test delle prestazioni. | No.<br>Il valore predefinito è 2048 (2 MB). |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromSAPHANA",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP HANA input dataset name>",
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
                "type": "SapHanaSource",
                "query": "<SQL query for SAP HANA>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Se si `RelationalSource` utilizza l'origine della copia digitata, questa è comunque supportata così com'è, mentre viene consigliato di utilizzare quella nuova in futuro.

## <a name="parallel-copy-from-sap-hana"></a>Copia parallela da SAP HANA

Il connettore DATA Factory SAP HANA fornisce il partizionamento dei dati incorporato per copiare i dati da SAP HANA in parallelo. Le opzioni di partizionamento dei dati sono disponibili nella tabella **Origine** dell'attività di copia.

![Screenshot delle opzioni di partizione](./media/connector-sap-hana/connector-sap-hana-partition-options.png)

Quando si abilita la copia partizionata, Data Factory esegue query parallele sull'origine SAP HANA per recuperare i dati in base alle partizioni. Il grado parallelo è [`parallelCopies`](copy-activity-performance.md#parallel-copy) controllato dall'impostazione dell'attività di copia. Ad esempio, se `parallelCopies` si imposta su quattro, Data Factory genera ed esegue contemporaneamente quattro query in base all'opzione e alle impostazioni della partizione specificata e ogni query recupera una parte dei dati da SAP HANA.

Si consiglia di abilitare la copia parallela con il partizionamento dei dati, soprattutto quando si inseriuna grandi quantità di dati da SAP HANA. Di seguito sono riportate le configurazioni consigliate per scenari diversi. Quando si copiano dati nell'archivio dati basato su file, si consiglia di scrivere in una cartella come più file (specificare solo il nome della cartella), nel qual caso le prestazioni sono migliori rispetto alla scrittura in un singolo file.

| Scenario                                           | Impostazioni suggerite                                           |
| -------------------------------------------------- | ------------------------------------------------------------ |
| Pieno carico da tavolo di grandi dimensioni.                        | **Opzione partizione**: Partizioni fisiche della tabella. <br><br/>Durante l'esecuzione, Data Factory rileva automaticamente il tipo di partizione fisica della tabella SAP HANA specificata e sceglie la strategia di partizione corrispondente:<br>- **Partizionamento intervallo**: Ottenere la colonna della partizione e gli intervalli di partizione definiti per la tabella, quindi copiare i dati in base all'intervallo. <br>- **Partizionamento hash**: utilizzare la chiave di partizione hash come colonna di partizione, quindi partizionare e copiare i dati in base a intervalli calcolati ADF. <br>- **Round-Robin Partitioning** o **Nessuna partizione**: Utilizzare la chiave primaria come colonna di partizione, quindi partizionare e copiare i dati in base a intervalli calcolati ADF. |
| Caricare grandi quantità di dati utilizzando una query personalizzata. | **Opzione partizione**: Partizione a intervallo dinamico.<br>**Query** `SELECT * FROM <TABLENAME> WHERE ?AdfHanaDynamicRangePartitionCondition AND <your_additional_where_clause>`: .<br>**Colonna partizione**: Specificare la colonna utilizzata per applicare la partizione dell'intervallo dinamico. <br><br>Durante l'esecuzione, Data Factory calcola innanzitutto gli intervalli di valori della colonna di partizione specificata, distribuisce uniformemente le righe in `?AdfHanaDynamicRangePartitionCondition` un numero di bucket in base al numero di valori di colonna di partizione distinti e all'impostazione della copia parallela ADF, quindi sostituisce con il filtraggio l'intervallo di valori della colonna di partizione per ogni partizione e invia a SAP HANA.<br><br>Se si desidera utilizzare più colonne come colonna di partizione, è possibile concatenare i valori di ogni `SELECT * FROM (SELECT *, CONCAT(<KeyColumn1>, <KeyColumn2>) AS PARTITIONCOLUMN FROM <TABLENAME>) WHERE ?AdfHanaDynamicRangePartitionCondition`colonna come una colonna nella query e specificarla come colonna di partizione in ADF, ad esempio . |

**Esempio: query con partizioni fisiche di una tabellaExample: query with physical partitions of a table**

```json
"source": {
    "type": "SapHanaSource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**Esempio: query con partizione di intervallo dinamicoExample: query with dynamic range partition**

```json
"source": {
    "type": "SapHanaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfHanaDynamicRangePartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "SapHanaDynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<Partition_column_name>"
    }
}
```

## <a name="data-type-mapping-for-sap-hana"></a>Mapping dei tipi di dati per SAP HANA

Quando si copiano dati da SAP HANA, vengono usati i mapping seguenti tra i tipi di dati di SAP HANA e i tipi di dati provvisori di Azure Data Factory. Vedere [Mapping dello schema e del tipo di dati](copy-activity-schema-and-type-mapping.md) per informazioni su come l'attività di copia esegue il mapping dello schema di origine e del tipo di dati al sink.

| Tipo di dati di SAP HANA | Tipo di dati provvisori di Data Factory |
| ------------------ | ------------------------------ |
| ALPHANUM           | string                         |
| bigint             | Int64                          |
| BINARY             | Byte[]                         |
| TESTO BIN            | string                         |
| BLOB               | Byte[]                         |
| BOOL               | Byte                           |
| CLOB               | string                         |
| DATE               | Datetime                       |
| DECIMAL            | Decimal                        |
| DOUBLE             | Double                         |
| FLOAT              | Double                         |
| INTEGER            | Int32                          |
| NCLOB              | string                         |
| NVARCHAR           | string                         |
| real               | Single                         |
| SECONDDATE         | Datetime                       |
| TESTO ABBREVIATO          | string                         |
| SMALLDECIMAL (DECIMALE DI PICCOLO NUMERO       | Decimal                        |
| SMALLINT           | Int16                          |
| TIPO STGEOMETRY     | Byte[]                         |
| STPOINTTYPE        | Byte[]                         |
| TEXT               | string                         |
| TIME               | TimeSpan                       |
| TINYINT            | Byte                           |
| VARCHAR            | string                         |
| timestamp          | Datetime                       |
| VARBINARY          | Byte[]                         |

## <a name="lookup-activity-properties"></a>Proprietà dell'attività di ricerca

Per informazioni dettagliate sulle proprietà, selezionare [Attività di ricerca](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

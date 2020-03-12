---
title: Copia i dati da SAP HANA
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
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2020
ms.locfileid: "79125992"
---
# <a name="copy-data-from-sap-hana-using-azure-data-factory"></a>Copiare dati da SAP HANA usando Azure Data Factory
> [!div class="op_single_selector" title1="Selezionare uSelezionare la versione del servizio di Azure Data Factory in uso:"]
> * [Versione 1](v1/data-factory-sap-hana-connector.md)
> * [Versione corrente](connector-sap-hana.md)

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da un database SAP HANA. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale sull'attività di copia.

>[!TIP]
>Per informazioni sul supporto generale di ADF sullo scenario di integrazione dei dati SAP, vedere l'articolo relativo all' [integrazione dei dati SAP con Azure Data Factory whitepaper](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) con informazioni dettagliate introduttive, comparsing e linee guida.

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo connettore SAP HANA è supportato per le attività seguenti:

- [Attività di copia](copy-activity-overview.md) con [matrice di origine/sink supportata](copy-activity-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)

È possibile copiare dati da un database SAP HANA in qualsiasi archivio dati di sink supportato. Per un elenco degli archivi dati supportati come origini/sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

In particolare, il connettore SAP HANA supporta:

- La copia di dati da qualsiasi versione del database SAP HANA.
- Copia di dati da **modelli di informazioni Hana** (ad esempio, viste analitiche e di calcolo) e **tabelle righe/colonne**.
- La copia di dati usando l'autenticazione **Di base** o **Windows**.
- Copia parallela da un'origine SAP HANA. Per informazioni dettagliate, vedere la sezione [copia parallela da SAP Hana](#parallel-copy-from-sap-hana) .

> [!TIP]
> Per copiare dati **in** un archivio dati SAP HANA, usare il connettore ODBC generico. Per i dettagli, vedere [Sink SAP HANA](connector-odbc.md#sap-hana-sink). Si noti che i servizi collegati per i connettori SAP HANA e ODBC sono associati a tipi diversi e pertanto non possono essere riusati.

## <a name="prerequisites"></a>Prerequisites

Per usare questo connettore SAP HANA, è necessario:

- Configurare un runtime di integrazione self-hosted. Per i dettagli, vedere l'articolo [Runtime di integrazione self-hosted](create-self-hosted-integration-runtime.md).
- Installare il driver ODBC di SAP HANA nel computer del runtime di integrazione. È possibile scaricare il driver ODBC di SAP HANA dall'[area per il download di software SAP](https://support.sap.com/swdc). Per cercare il driver, usare la parola chiave **SAP HANA CLIENT for Windows**.

## <a name="getting-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire entità di Data Factory specifiche per il connettore SAP HANA.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato di SAP HANA sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su: **SapHana** | Sì |
| connectionString | Specificare le informazioni necessarie per connettersi al SAP HANA usando l' **autenticazione di base** o **l'autenticazione di Windows**. Vedere gli esempi seguenti.<br>In stringa di connessione, il server/porta è obbligatorio (la porta predefinita è 30015) e il nome utente e la password sono obbligatori quando si usa l'autenticazione di base. Per altre impostazioni avanzate, vedere [SAP Hana proprietà di connessione ODBC](<https://help.sap.com/viewer/0eec0d68141541d1b07893a39944924e/2.0.02/en-US/7cab593774474f2f8db335710b2f5c50.html>)<br/>È anche possibile inserire la password in Azure Key Vault ed estrarre la configurazione della password dalla stringa di connessione. Per informazioni dettagliate, vedere [archiviare le credenziali in Azure Key Vault](store-credentials-in-key-vault.md) articolo. | Sì |
| userName | Specificare il nome utente quando si utilizza l'autenticazione di Windows. Esempio: `user@domain.com` | No |
| password | Specifica la password per l'account utente. Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Azure Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). | No |
| connectVia | Il [runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È necessario un runtime di integrazione self-hosted come indicato in [Prerequisiti](#prerequisites). |Sì |

**Esempio: uso dell'autenticazione di base**

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

**Esempio: uso dell'autenticazione di Windows**

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

Se si usa SAP HANA servizio collegato con il payload seguente, è ancora supportato così com'è, mentre si consiglia di usare quello nuovo in futuro.

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

Per copiare dati da SAP HANA, sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà Type del set di dati deve essere impostata su: **SapHanaTable** | Sì |
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

Se si usa `RelationalTable` DataSet tipizzato, è ancora supportato così com'è, mentre si consiglia di usare quello nuovo in futuro.

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine SAP HANA.

### <a name="sap-hana-as-source"></a>SAP HANA come origine

>[!TIP]
>Per inserire i dati da SAP HANA in modo efficiente usando il partizionamento dei dati, vedere la sezione relativa alla [copia parallela da SAP Hana](#parallel-copy-from-sap-hana) .

Per copiare dati da SAP HANA, nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà Type dell'origine dell'attività di copia deve essere impostata su: **SapHanaSource** | Sì |
| query | Specifica la query SQL che consente di leggere i dati dall'istanza di SAP HANA. | Sì |
| partitionOptions | Specifica le opzioni di partizionamento dei dati utilizzate per inserire dati da SAP HANA. Per ulteriori informazioni, vedere la sezione [copia parallela dalla SAP Hana](#parallel-copy-from-sap-hana) .<br>Consenti valori: **None** (impostazione predefinita), **PhysicalPartitionsOfTable**, **SapHanaDynamicRange**. Per ulteriori informazioni, vedere la sezione [copia parallela dalla SAP Hana](#parallel-copy-from-sap-hana) . `PhysicalPartitionsOfTable` può essere utilizzato solo per la copia di dati da una tabella, ma non da query. <br>Quando è abilitata un'opzione di partizione (ovvero non `None`), il grado di parallelismo per caricare simultaneamente i dati da SAP HANA è controllato dall'impostazione [`parallelCopies`](copy-activity-performance.md#parallel-copy) sull'attività di copia. | False |
| partitionSettings | Consente di specificare il gruppo di impostazioni per il partizionamento dei dati.<br>Applica quando l'opzione di partizione è `SapHanaDynamicRange`. | False |
| partitionColumnName | Consente di specificare il nome della colonna di origine che verrà utilizzata dalla partizione per la copia parallela. Se non è specificato, l'indice o la chiave primaria della tabella vengono rilevati automaticamente e utilizzati come colonna della partizione.<br>Applicare quando l'opzione di partizione è `SapHanaDynamicRange`. Se si utilizza una query per recuperare i dati di origine, associare `?AdfHanaDynamicRangePartitionCondition` nella clausola WHERE. Vedere l'esempio in [copia parallela dalla sezione SAP Hana](#parallel-copy-from-sap-hana) . | Sì quando si usa `SapHanaDynamicRange` partizione. |
| packetSize | Specifica le dimensioni del pacchetto di rete (in kilobyte) per suddividere i dati in più blocchi. Se è presente una grande quantità di dati da copiare, l'aumento delle dimensioni del pacchetto può aumentare la velocità di lettura da SAP HANA nella maggior parte dei casi. Il test delle prestazioni è consigliato per la regolazione delle dimensioni del pacchetto. | No.<br>Il valore predefinito è 2048 (2MB). |

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

Se si usa `RelationalSource` origine della copia tipizzata, questo è ancora supportato così com'è, mentre si consiglia di usare quello nuovo in futuro.

## <a name="parallel-copy-from-sap-hana"></a>Copia parallela da SAP HANA

Il connettore Data Factory SAP HANA fornisce il partizionamento dei dati predefinito per copiare i dati da SAP HANA in parallelo. È possibile trovare le opzioni di partizionamento dei dati nella tabella di **origine** dell'attività di copia.

![Screenshot delle opzioni di partizione](./media/connector-sap-hana/connector-sap-hana-partition-options.png)

Quando si Abilita la copia partizionata, Data Factory esegue query parallele sull'origine SAP HANA per recuperare i dati in base alle partizioni. Il grado parallelo è controllato dall'impostazione del [`parallelCopies`](copy-activity-performance.md#parallel-copy) sull'attività di copia. Se, ad esempio, si imposta `parallelCopies` su quattro, Data Factory genera ed esegue quattro query in base all'opzione di partizione specificata e alle impostazioni e ogni query recupera una porzione di dati dal SAP HANA.

Si consiglia di abilitare la copia parallela con il partizionamento dei dati, specialmente quando si inseriscono grandi quantità di dati dal SAP HANA. Di seguito sono elencate le configurazioni consigliate per diversi scenari. Quando si copiano dati in un archivio dati basato su file, è consigliabile scrivere in una cartella come più file (specificare solo il nome della cartella), nel qual caso le prestazioni sono migliori rispetto alla scrittura in un singolo file.

| Scenario                                           | Impostazioni consigliate                                           |
| -------------------------------------------------- | ------------------------------------------------------------ |
| Caricamento completo da una tabella di grandi dimensioni.                        | **Opzione partition**: partizioni fisiche della tabella. <br><br/>Durante l'esecuzione, Data Factory rileva automaticamente il tipo di partizione fisica della tabella SAP HANA specificata e sceglie la strategia di partizione corrispondente:<br>**partizionamento - intervallo**: ottenere la colonna di partizione e gli intervalli di partizioni definiti per la tabella, quindi copiare i dati in base all'intervallo. <br>- **partizionamento hash**: usare la chiave di partizione hash come colonna di partizione, quindi partizionare e copiare i dati in base agli intervalli calcolati di ADF. <br>- il **partizionamento Round Robin** o **Nessuna partizione**: usare la chiave primaria come colonna di partizione, quindi partizionare e copiare i dati in base agli intervalli calcolati di ADF. |
| Caricare grandi quantità di dati tramite una query personalizzata. | **Opzione partition**: partizione a intervalli dinamici.<br>**Query**: `SELECT * FROM <TABLENAME> WHERE ?AdfHanaDynamicRangePartitionCondition AND <your_additional_where_clause>`.<br>**Colonna partizione**: specificare la colonna utilizzata per applicare la partizione a intervalli dinamici. <br><br>Durante l'esecuzione Data Factory calcola innanzitutto gli intervalli di valori della colonna di partizione specificata, distribuendo in modo uniforme le righe in un numero di bucket in base al numero di valori di colonna di partizione distinti e all'impostazione di copia parallela ADF, quindi sostituisce `?AdfHanaDynamicRangePartitionCondition` con il filtro dell'intervallo di valori della colonna di partizione per ogni partizione e invia al SAP HANA.<br><br>Se si desidera utilizzare più colonne come colonna di partizione, è possibile concatenare i valori di ogni colonna come una colonna della query e specificarla come colonna di partizione in ADF, ad esempio `SELECT * FROM (SELECT *, CONCAT(<KeyColumn1>, <KeyColumn2>) AS PARTITIONCOLUMN FROM <TABLENAME>) WHERE ?AdfHanaDynamicRangePartitionCondition`. |

**Esempio: query con partizioni fisiche di una tabella**

```json
"source": {
    "type": "SapHanaSource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**Esempio: query con partizione a intervalli dinamici**

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
| Bintext            | string                         |
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
| SHORTTEXT          | string                         |
| SMALLDECIMAL       | Decimal                        |
| SMALLINT           | Int16                          |
| STGEOMETRYTYPE     | Byte[]                         |
| STPOINTTYPE        | Byte[]                         |
| TEXT               | string                         |
| TIME               | TimeSpan                       |
| TINYINT            | Byte                           |
| VARCHAR            | string                         |
| timestamp          | Datetime                       |
| VARBINARY          | Byte[]                         |

## <a name="lookup-activity-properties"></a>Proprietà attività di ricerca

Per informazioni dettagliate sulle proprietà, controllare l' [attività di ricerca](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

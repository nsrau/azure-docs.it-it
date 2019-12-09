---
title: Copiare dati da origini ODBC utilizzando Azure Data Factory
description: Informazioni su come copiare dati da origini OData in archivi dati di sink supportati usando un'attività di copia in una pipeline di Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: b94dbb81b2ab5b7e4421357ee81d6c3ea8e8d3c0
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74912494"
---
# <a name="copy-data-from-and-to-odbc-data-stores-using-azure-data-factory"></a>Copiare i dati da e verso archivi dati ODBC con Azure Data Factory
> [!div class="op_single_selector" title1="Selezionare uSelezionare la versione del servizio di Azure Data Factory in uso:"]
> * [Versione 1](v1/data-factory-odbc-connector.md)
> * [Versione corrente](connector-odbc.md)

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da e verso un archivio dati di ODBC. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale sull'attività di copia.

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo connettore ODBC è supportato per le attività seguenti:

- [Attività di copia](copy-activity-overview.md) con [matrice di origine/sink supportata](copy-activity-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)

È possibile copiare i dati da un'origine ODBC in qualsiasi archivio dati sink supportato o da qualsiasi archivio dati di origine supportato in un sink di ODBC. Per un elenco degli archivi dati supportati come origini/sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

In particolare, il connettore ODBC supporta la copia dei dati da o verso **qualsiasi archivio dati compatibile con ODBC** tramite l'autenticazione di **base** o **anonima**.

## <a name="prerequisites"></a>Prerequisiti

Per usare il connettore ODBC è necessario:

- Configurare un runtime di integrazione self-hosted. Per i dettagli, vedere l'articolo [Runtime di integrazione self-hosted](create-self-hosted-integration-runtime.md).
- Installare il driver ODBC per l'archivio dati nel computer del runtime di integrazione.

## <a name="getting-started"></a>Inizia ora

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire entità di data factory specifiche per il connettore ODBC.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato ODBC sono supportate le proprietà seguenti:

| Proprietà | Description | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su **ODBC** | SÌ |
| connectionString | Stringa di connessione ad esclusione della parte relativa alle credenziali. È possibile specificare la stringa di connessione con un modello come `"Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;"`, o usare il sistema DSN, ovvero il nome dell'origine dati, configurato nel computer Integration Runtime con `"DSN=<name of the DSN on IR machine>;"`. È necessario comunque specificare la parte delle credenziali nel servizio collegato in base alle esigenze.<br>Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Azure Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md).| SÌ |
| authenticationType | Tipo di autenticazione usato per connettersi all'archivio dati ODBC.<br/>I valori consentiti sono **Base** e **Anonimo**. | SÌ |
| userName | Specificare il nome utente se si usa l'autenticazione di base. | No |
| password | Specificare la password per l'account utente specificato per userName. Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Azure Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). | No |
| credential | La parte delle credenziali di accesso della stringa di connessione specificata nel formato di valore della proprietà specifico del driver. Esempio: `"RefreshToken=<secret refresh token>;"`. Contrassegnare questo campo come SecureString. | No |
| connectVia | Il [runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È necessario un runtime di integrazione self-hosted come indicato in [Prerequisiti](#prerequisites). |SÌ |

**Esempio 1: uso dell'autenticazione di base**

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "<connection string>"
            },
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

**Esempio 2: uso dell'autenticazione anonima**

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "<connection string>"
            },
            "authenticationType": "Anonymous",
            "credential": {
                "type": "SecureString",
                "value": "RefreshToken=<secret refresh token>;"
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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sui [set di dati](concepts-datasets-linked-services.md). Questa sezione presenta un elenco delle proprietà supportate dal set di dati ODBC.

Per copiare dati da/in un archivio dati compatibile con ODBC, sono supportate le proprietà seguenti:

| Proprietà | Description | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà Type del set di dati deve essere impostata su: **OdbcTable** | SÌ |
| tableName | Nome della tabella nell'archivio dati ODBC. | No per l'origine (se nell'origine dell'attività è specificato "query");<br/>sì per il sink |

**Esempio**

```json
{
    "name": "ODBCDataset",
    "properties": {
        "type": "OdbcTable",
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<ODBC linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<table name>"
        }
    }
}
```

Se si usa `RelationalTable` DataSet tipizzato, è ancora supportato così com'è, mentre si consiglia di usare quello nuovo in futuro.

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine di ODBC.

### <a name="odbc-as-source"></a>ODBC come origine

Per copiare dati da un archivio dati compatibile con ODBC, nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Description | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà Type dell'origine dell'attività di copia deve essere impostata su: **OdbcSource** | SÌ |
| query | Usare la query SQL personalizzata per leggere i dati. Ad esempio: `"SELECT * FROM MyTable"`. | No (se nel set di dati è specificato "tableName") |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromODBC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ODBC input dataset name>",
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
                "type": "OdbcSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Se si usa `RelationalSource` origine tipizzata, questo è ancora supportato così com'è, mentre si consiglia di usare quello nuovo in futuro.

### <a name="odbc-as-sink"></a>ODBC come sink

Per copiare i dati nell'archivio dati compatibile con ODBC, impostare il tipo di sink nell'attività di copia su **OdbcSink**. Nella sezione **sink** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Description | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del sink dell'attività di copia deve essere impostata su: **OdbcSink** | SÌ |
| writeBatchTimeout |Tempo di attesa per l'operazione di inserimento batch da completare prima del timeout.<br/>I valori consentiti sono: intervallo di tempo. Ad esempio: "00:30:00" (30 minuti). |No |
| writeBatchSize |Inserisce dati nella tabella SQL quando la dimensione del buffer raggiunge writeBatchSize.<br/>I valori consentiti sono integer, ovvero il numero di righe. |No (l'impostazione predefinita è 0 - rilevamento automatico) |
| preCopyScript |Specificare una query SQL per l'attività di copia da eseguire prima di scrivere i dati nell'archivio dati in ogni esecuzione. È possibile usare questa proprietà per pulire i dati precaricati. |No |

> [!NOTE]
> Per "writeBatchSize", se non impostato tramite il rilevamento automatico, l'attività di copia rileva prima se il driver supporta le operazioni batch e in caso affermativo lo imposta su 10.000, in caso contrario lo imposta su 1. Se si imposta in modo esplicito un valore diverso da 0, l'attività di copia rispetta il valore, ma non riesce a eseguire il runtime se il driver non supporta le operazioni batch.

**Esempio:**

```json
"activities":[
    {
        "name": "CopyToODBC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ODBC output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "OdbcSink",
                "writeBatchSize": 100000
            }
        }
    }
]
```

## <a name="sap-hana-sink"></a>Sink SAP HANA

>[!NOTE]
>Per copiare i dati dall'archivio dati di SAP HANA, fare riferimento al [connettore SAP HANA](connector-sap-hana.md) nativo. Per copiare dati in SAP HANA, seguire questa istruzione per usare il connettore ODBC. I servizi collegati per i connettori SAP HANA e ODBC sono associati a tipi diversi e pertanto non possono essere riusati.
>

È possibile copiare i dati dal database SAP HANA usando il connettore ODBC generico.

Configurare un runtime di integrazione self-hosted in un computer con accesso all'archivio dati. Il runtime di integrazione usa il driver ODBC per SAP HANA per eseguire la connessione all'archivio dati. Installare quindi il driver se non è già installato nel computer. Per altre informazioni, vedere la sezione [Prerequisiti](#prerequisites).

Prima di usare il sink SAP HANA in una soluzione Data Factory, verificare che Integration Runtime sia in grado di connettersi all'archivio dati usando le istruzioni nella sezione [Risoluzione dei problemi di connettività](#troubleshoot-connectivity-issues).

Creare un servizio collegato ODBC per collegare un archivio dati SAP HANA a una data factory di Azure come illustrato nell'esempio seguente:

```json
{
    "name": "SAPHANAViaODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Driver={HDBODBC};servernode=<HANA server>.clouddatahub-int.net:30015"
            },
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

Leggere l'articolo dall'inizio per avere una panoramica dettagliata dell'uso degli archivi dati ODBC come archivi dati di origine o sink in un'operazione di copia.

## <a name="lookup-activity-properties"></a>Proprietà attività di ricerca

Per informazioni dettagliate sulle proprietà, controllare l' [attività di ricerca](control-flow-lookup-activity.md).


## <a name="troubleshoot-connectivity-issues"></a>Risoluzione dei problemi di connettività

Per risolvere i problemi di connessione, usare la scheda **Diagnostica** di **Gestione configurazione di Runtime di integrazione**.

1. Avviare **Gestione configurazione di Runtime di integrazione**.
2. Passare alla scheda **Diagnostica** .
3. Nella sezione "Connessione di test" selezionare il **tipo** di archivio dati, ovvero il servizio collegato.
4. Specificare la **stringa di connessione** usata per la connessione all'archivio dati, scegliere l'**autenticazione** e immettere il **nome utente**, la **password** e/o le **credenziali**.
5. Fare clic su **Test connessione** per testare la connessione all'archivio dati.

## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md##supported-data-stores-and-formats).

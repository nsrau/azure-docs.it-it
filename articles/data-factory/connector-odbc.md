---
title: Copiare dati da origini dati ODBC usando Azure Data Factory | Microsoft Docs
description: "Informazioni su come copiare dati da origini OData in archivi dati sink supportati usando un'attività di copia in una pipeline di Azure Data Factory."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: jingwang
ms.openlocfilehash: 9e65735ed6d19c8b94496fc3d3445e3a9dca2b9d
ms.sourcegitcommit: 963e0a2171c32903617d883bb1130c7c9189d730
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2017
---
# <a name="copy-data-from-and-to-odbc-data-stores-using-azure-data-factory"></a>Copiare i dati da e verso archivi dati ODBC con Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versione 1 - Disponibilità generale](v1/data-factory-odbc-connector.md)
> * [Versione 2 - Anteprima](connector-odbc.md)

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da e verso un archivio dati di ODBC. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale sull'attività di copia.

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere [Connettore ODBC in V1](v1/data-factory-odata-connector.md).

## <a name="supported-scenarios"></a>Scenari supportati

È possibile copiare i dati da un'origine ODBC in qualsiasi archivio dati sink supportato o da qualsiasi archivio dati di origine supportato in un sink di ODBC. Per un elenco degli archivi dati supportati come origini/sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

In particolare, il connettore ODBC supporta la copia dei dati da o verso **qualsiasi archivio dati compatibile con ODBC** tramite l'autenticazione di **base** o **anonima**.

## <a name="prerequisites"></a>Prerequisiti

Per usare il connettore ODBC è necessario:

- Configurare un runtime di integrazione self-hosted. Per i dettagli, vedere l'articolo [Runtime di integrazione self-hosted](create-self-hosted-integration-runtime.md).
- Installare il driver ODBC per l'archivio dati nel computer del runtime di integrazione.

## <a name="getting-started"></a>introduttiva
È possibile creare una pipeline con l'attività di copia usando .NET SDK, Python SDK, Azure PowerShell, l'API REST o il modello Azure Resource Manager. Vedere l'[esercitazione sull'attività di copia](quickstart-create-data-factory-dot-net.md) per le istruzioni dettagliate sulla creazione di una pipeline con un'attività di copia.

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire entità di data factory specifiche per il connettore ODBC.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato ODBC sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su **ODBC** | Sì |
| connectionString | Stringa di connessione ad esclusione della parte relativa alle credenziali. È possibile specificare la stringa di connessione con un modello come `"Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;"`, o usare il sistema DSN, ovvero il nome dell'origine dati, configurato nel computer Integration Runtime con `"DSN=<name of the DSN on IR machine>;"`. È necessario comunque specificare la parte delle credenziali nel servizio collegato in base alle esigenze.| Sì |
| authenticationType | Tipo di autenticazione usato per connettersi all'archivio dati ODBC.<br/>I valori consentiti sono **Base** e **Anonimo**. | Sì |
| userName | Specificare il nome utente se si usa l'autenticazione di base. | No |
| password | Specificare la password per l'account utente specificato per userName. Contrassegnare questo campo come SecureString. | No |
| credential | La parte delle credenziali di accesso della stringa di connessione specificata nel formato di valore della proprietà specifico del driver. Esempio: `"RefreshToken=<secret refresh token>;"`. Contrassegnare questo campo come SecureString. | No |
| connectVia | Il [runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È necessario un runtime di integrazione self-hosted come indicato in [Prerequisiti](#prerequisites). |Sì |

**Esempio 1: uso dell'autenticazione di base**

```json
{
    "name": "ODBCLinkedService",
    "properties":
    {
        "type": "Odbc",
        "typeProperties":
        {
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
    "properties":
    {
        "type": "Odbc",
        "typeProperties":
        {
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

## <a name="dataset-properties"></a>Proprietà dei set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sui set di dati. Questa sezione presenta un elenco delle proprietà supportate dal set di dati ODBC.

Per copiare dati da e verso un archivio dati compatibile con ODBC, impostare la proprietà type del set di dati su **RelationalTable**. Sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| type | La proprietà type del set di dati deve essere impostata su: **RelationalTable** | Sì |
| tableName | Nome della tabella nell'archivio dati ODBC. | No per l'origine (se nell'origine dell'attività è specificato "query");<br/>sì per il sink |

**Esempio**

```json
{
    "name": "ODBCDataset",
    "properties":
    {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<ODBC linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties":
        {
            "tableName": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine di ODBC.

### <a name="odbc-as-source"></a>ODBC come origine

Per copiare dati dall'archivio dati compatibile con ODBC, impostare il tipo di origine nell'attività di copia su **RelationalSource**. Nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| type | La proprietà type dell'origine dell'attività di copia deve essere impostata su: **RelationalSource** | Sì |
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
                "type": "RelationalSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="odbc-as-sink"></a>ODBC come sink

Per copiare i dati nell'archivio dati compatibile con ODBC, impostare il tipo di sink nell'attività di copia su **OdbcSink**. Nella sezione **sink** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| type | La proprietà type dell'origine dell'attività di copia deve essere impostata su **OdbcSink** | Sì |
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

## <a name="ibm-informix-source"></a>Origine IBM Informix

È possibile copiare i dati dal database IBM Informix usando il connettore ODBC generico.

Configurare un runtime di integrazione self-hosted in un computer con accesso all'archivio dati. Integration Runtime usa il driver ODBC per Informix per eseguire la connessione all'archivio dati. Installare quindi il driver se non è già installato nel computer. Ad esempio, è possibile usare i driver "DRIVER ODBC INFORMIX IBM (64 bit)". Per altre informazioni, vedere la sezione [Prerequisiti](#prerequisites).

Prima di usare l'origine Informix in una soluzione Data Factory, verificare che Integration Runtime sia in grado di connettersi all'archivio dati usando le istruzioni nella sezione [Risoluzione dei problemi di connettività](#troubleshoot-connectivity-issues).

Creare un servizio collegato ODBC per collegare un archivio dati IBM Informix a una data factory di Azure come illustrato nell'esempio seguente:

```json
{
    "name": "InformixLinkedService",
    "properties":
    {
        "type": "Odbc",
        "typeProperties":
        {
            "connectionString": {
                "type": "SecureString",
                "value": "<Informix connection string or DSN>"
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

## <a name="microsoft-access-source"></a>Origine Microsoft Access

È possibile copiare i dati dal database Microsoft Access usando il connettore ODBC generico.

Configurare un runtime di integrazione self-hosted in un computer con accesso all'archivio dati. Integration Runtime usa il driver ODBC per eseguire la connessione di Microsoft Access all'archivio dati. Installare quindi il driver se non è già installato nel computer. Per altre informazioni, vedere la sezione [Prerequisiti](#prerequisites).

Prima di usare l'origine Microsoft Access in una soluzione Data Factory, verificare che Integration Runtime sia in grado di connettersi all'archivio dati usando le istruzioni nella sezione [Risoluzione dei problemi di connettività](#troubleshoot-connectivity-issues).

Creare un servizio collegato ODBC per collegare un database Microsoft Access a una data factory di Azure come illustrato nell'esempio seguente:

```json
{
    "name": "MicrosoftAccessLinkedService",
    "properties":
    {
        "type": "Odbc",
        "typeProperties":
        {
            "connectionString": {
                "type": "SecureString",
                "value": "Driver={Microsoft Access Driver (*.mdb, *.accdb)};Dbq=<path to your DB file e.g. C:\\mydatabase.accdb>;"
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

## <a name="ge-historian-source"></a>Origine GE Historian

È possibile copiare i dati da GE Historian usando il connettore ODBC generico.

Configurare un runtime di integrazione self-hosted in un computer con accesso all'archivio dati. Il runtime di integrazione usa il driver ODBC per GE Historian ed esegue la connessione all'archivio dati. Installare quindi il driver se non è già installato nel computer. Per altre informazioni, vedere la sezione [Prerequisiti](#prerequisites).

Prima di usare l'origine GE Historian in una soluzione Data Factory, verificare se Integration Runtime è in grado di connettersi all'archivio dati usando le istruzioni nella sezione [Risoluzione dei problemi di connettività](#troubleshoot-connectivity-issues).

Creare un servizio collegato ODBC per collegare un database Microsoft Access a una data factory di Azure come illustrato nell'esempio seguente:

```json
{
    "name": "HistorianLinkedService",
    "properties":
    {
        "type": "Odbc",
        "typeProperties":
        {
            "connectionString": {
                "type": "SecureString",
                "value": "<GE Historian store connection string or DSN>"
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

## <a name="sap-hana-sink"></a>Sink SAP HANA

>[!NOTE]
>Per copiare i dati dall'archivio dati di SAP HANA, fare riferimento al [connettore SAP HANA](connector-sap-hana.md) nativo. Per copiare dati in SAP HANA, seguire questa istruzione per usare il connettore ODBC. Si noti che i servizi collegati per i connettori SAP HANA e ODBC sono associati a tipi diversi e pertanto non possono essere riusati.
>

È possibile copiare i dati dal database SAP HANA usando il connettore ODBC generico.

Configurare un runtime di integrazione self-hosted in un computer con accesso all'archivio dati. Il runtime di integrazione usa il driver ODBC per SAP HANA per eseguire la connessione all'archivio dati. Installare quindi il driver se non è già installato nel computer. Per altre informazioni, vedere la sezione [Prerequisiti](#prerequisites).

Prima di usare il sink SAP HANA in una soluzione Data Factory, verificare che Integration Runtime sia in grado di connettersi all'archivio dati usando le istruzioni nella sezione [Risoluzione dei problemi di connettività](#troubleshoot-connectivity-issues).

Creare un servizio collegato ODBC per collegare un archivio dati SAP HANA a una data factory di Azure come illustrato nell'esempio seguente:

```json
{
    "name": "SAPHANAViaODBCLinkedService",
    "properties":
    {
        "type": "Odbc",
        "typeProperties":
        {
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

## <a name="troubleshoot-connectivity-issues"></a>Risoluzione dei problemi di connettività

Per risolvere i problemi di connessione, usare la scheda **Diagnostica** di **Gestione configurazione di Runtime di integrazione**.

1. Avviare **Gestione configurazione di Runtime di integrazione**.
2. Passare alla scheda **Diagnostica** .
3. Nella sezione "Connessione di test" selezionare il **tipo** di archivio dati, ovvero il servizio collegato.
4. Specificare la **stringa di connessione** usata per la connessione all'archivio dati, scegliere l'**autenticazione** e immettere il **nome utente**, la **password** e/o le **credenziali**.
5. Fare clic su **Test connessione** per testare la connessione all'archivio dati.

## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md##supported-data-stores-and-formats).